#include <iostream>

#include <stdio.h>

#include <stdlib.h>      /* for malloc, free */

#include <ctype.h>       /* for isupper, islower, tolower */

#include<cmath>

#include<algorithm>

//#include<process.h>

#include<vector>

#include<string>

#include<string.h>

#include<deque>

#include<map>   //for multi-map used in frequency function

#include<fstream>

#include <stdlib.h>  /* for malloc, free */

#include <string.h>  /* for memcmp, memmove */

#include <dirent.h> //for some directory manipulations

#include<sstream>

#include<stack>



using namespace std;



vector<int> rank1;

vector<int> rank2;

vector<int> rank3;

vector<int> rank4;



/* A Count of Matrix Dimensions */

int MATRIX_ROW=0;

int MATRIX_COL=0;



void enque(int,int);

void enque_new(int,int);//to push the values i,j at which pcc is highest in the queue

int sequence[1000000];

int sequence1[1000000]; //array in which the order of sequence is stored!

int rear=0;

int ct=0;

int ct1=0;/*

  when this code will be merged with main program in that case we have to declare a ct

   (global variable) that will ct the number of sentences and then we would use that ct in the code

    .. then we will comment this statement*/



/**********************STEMMER STARTS HERE******************************************/



struct stemmer;



extern struct stemmer * create_stemmer(void);

extern void free_stemmer(struct stemmer * z);



extern int stem(struct stemmer * z, char * b, int k);





#define TRUE 1

#define FALSE 0



/* stemmer is a structure for a few local bits of data,

*/



struct stemmer

{

    char * b;       /* buffer for word to be stemmed */

    int k;          /* offset to the end of the string */

    int j;          /* a general offset into the string */

};





/* Member b is a buffer holding a word to be stemmed. The letters are in

   b[0], b[1] ... ending at b[z->k]. Member k is readjusted downwards as

   the stemming progresses. Zero termination is not in fact used in the

   algorithm.

   Typical usage is:



       struct stemmer * z = create_stemmer();

       char b[] = "pencils";

       int res = stem(z, b, 6);

           /- stem the 7 characters of b[0] to b[6]. The result, res,

              will be 5 (the 's' is removed). -/

       free_stemmer(z);

*/





extern struct stemmer * create_stemmer(void)

{

    return (struct stemmer *) malloc(sizeof(struct stemmer));

    /* assume malloc succeeds */

}



extern void free_stemmer(struct stemmer * z)

{

    free(z);

}





/* cons(z, i) is TRUE <=> b[i] is a consonant. ('b' means 'z->b', but here

   and below we drop 'z->' in comments.

*/



static int cons(struct stemmer * z, int i)

{

    switch (z->b[i])

    {

    case 'a':

    case 'e':

    case 'i':

    case 'o':

    case 'u':

        return FALSE;

    case 'y':

        return (i == 0) ? TRUE : !cons(z, i - 1);

    default:

        return TRUE;

    }

}



/* m(z) measures the number of consonant sequences between 0 and j. if c is

   a consonant sequence and v a vowel sequence, and <..> indicates arbitrary

   presence,



      <c><v>       gives 0

      <c>vc<v>     gives 1

      <c>vcvc<v>   gives 2

      <c>vcvcvc<v> gives 3

      ....

*/



static int m(struct stemmer * z)

{

    int n = 0;

    int i = 0;

    int j = z->j;

    while(TRUE)

    {

        if (i > j) return n;

        if (! cons(z, i)) break;

        i++;

    }

    i++;

    while(TRUE)

    {

        while(TRUE)

        {

            if (i > j) return n;

            if (cons(z, i)) break;

            i++;

        }

        i++;

        n++;

        while(TRUE)

        {

            if (i > j) return n;

            if (! cons(z, i)) break;

            i++;

        }

        i++;

    }

}



/* vowelinstem(z) is TRUE <=> 0,...j contains a vowel */



static int vowelinstem(struct stemmer * z)

{

    int j = z->j;

    int i;

    for (i = 0; i <= j; i++) if (! cons(z, i)) return TRUE;

    return FALSE;

}



/* doublec(z, j) is TRUE <=> j,(j-1) contain a double consonant. */



static int doublec(struct stemmer * z, int j)

{

    char * b = z->b;

    if (j < 1) return FALSE;

    if (b[j] != b[j - 1]) return FALSE;

    return cons(z, j);

}



/* cvc(z, i) is TRUE <=> i-2,i-1,i has the form consonant - vowel - consonant

   and also if the second c is not w,x or y. this is used when trying to

   restore an e at the end of a short word. e.g.



      cav(e), lov(e), hop(e), crim(e), but

      snow, box, tray.



*/



static int cvc(struct stemmer * z, int i)

{

    if (i < 2 || !cons(z, i) || cons(z, i - 1) || !cons(z, i - 2)) return FALSE;

    {

        int ch = z->b[i];

        if (ch  == 'w' || ch == 'x' || ch == 'y') return FALSE;

    }

    return TRUE;

}



/* ends(z, s) is TRUE <=> 0,...k ends with the string s. */



static int ends(struct stemmer * z, char * s)

{

    int length = s[0];

    char * b = z->b;

    int k = z->k;

    if (s[length] != b[k]) return FALSE; /* tiny speed-up */

    if (length > k + 1) return FALSE;

    if (memcmp(b + k - length + 1, s + 1, length) != 0) return FALSE;

    z->j = k-length;

    return TRUE;

}



/* setto(z, s) sets (j+1),...k to the characters in the string s, readjusting

   k. */



static void setto(struct stemmer * z, char * s)

{

    int length = s[0];

    int j = z->j;

    memmove(z->b + j + 1, s + 1, length);

    z->k = j+length;

}



/* r(z, s) is used further down. */



static void r(struct stemmer * z, char * s)

{

    if (m(z) > 0) setto(z, s);

}



/* step1ab(z) gets rid of plurals and -ed or -ing. e.g.



       caresses  ->  caress

       ponies    ->  poni

       ties      ->  ti

       caress    ->  caress

       cats      ->  cat



       feed      ->  feed

       agreed    ->  agree

       disabled  ->  disable



       matting   ->  mat

       mating    ->  mate

       meeting   ->  meet

       milling   ->  mill

       messing   ->  mess



       meetings  ->  meet



*/



static void step1ab(struct stemmer * z)

{

    char * b = z->b;

    if (b[z->k] == 's')

    {

        if (ends(z, "\04" "sses")) z->k -= 2;

        else if (ends(z, "\03" "ies")) setto(z, "\01" "i");

        else if (b[z->k - 1] != 's') z->k--;

    }

    if (ends(z, "\03" "eed"))

    {

        if (m(z) > 0) z->k--;

    }

    else if ((ends(z, "\02" "ed") || ends(z, "\03" "ing")) && vowelinstem(z))

    {

        z->k = z->j;

        if (ends(z, "\02" "at")) setto(z, "\03" "ate");

        else if (ends(z, "\02" "bl")) setto(z, "\03" "ble");

        else if (ends(z, "\02" "iz")) setto(z, "\03" "ize");

        else if (doublec(z, z->k))

        {

            z->k--;

            {

                int ch = b[z->k];

                if (ch == 'l' || ch == 's' || ch == 'z') z->k++;

            }

        }

        else if (m(z) == 1 && cvc(z, z->k)) setto(z, "\01" "e");

    }

}



/* step1c(z) turns terminal y to i when there is another vowel in the stem. */



static void step1c(struct stemmer * z)

{

    if (ends(z, "\01" "y") && vowelinstem(z)) z->b[z->k] = 'i';

}





/* step2(z) maps double suffices to single ones. so -ization ( = -ize plus

   -ation) maps to -ize etc. note that the string before the suffix must give

   m(z) > 0. */



static void step2(struct stemmer * z)

{

    switch (z->b[z->k-1])

    {

    case 'a':

        if (ends(z, "\07" "ational"))

        {

            r(z, "\03" "ate");

            break;

        }

        if (ends(z, "\06" "tional"))

        {

            r(z, "\04" "tion");

            break;

        }

        break;

    case 'c':

        if (ends(z, "\04" "enci"))

        {

            r(z, "\04" "ence");

            break;

        }

        if (ends(z, "\04" "anci"))

        {

            r(z, "\04" "ance");

            break;

        }

        break;

    case 'e':

        if (ends(z, "\04" "izer"))

        {

            r(z, "\03" "ize");

            break;

        }

        break;

    case 'l':

        if (ends(z, "\03" "bli"))

        {

            r(z, "\03" "ble");    /*-DEPARTURE-*/

            break;

        }



        /* To match the published algorithm, replace this line with

           case 'l': if (ends(z, "\04" "abli")) { r(z, "\04" "able"); break; } */



        if (ends(z, "\04" "alli"))

        {

            r(z, "\02" "al");

            break;

        }

        if (ends(z, "\05" "entli"))

        {

            r(z, "\03" "ent");

            break;

        }

        if (ends(z, "\03" "eli"))

        {

            r(z, "\01" "e");

            break;

        }

        if (ends(z, "\05" "ousli"))

        {

            r(z, "\03" "ous");

            break;

        }

        break;

    case 'o':

        if (ends(z, "\07" "ization"))

        {

            r(z, "\03" "ize");

            break;

        }

        if (ends(z, "\05" "ation"))

        {

            r(z, "\03" "ate");

            break;

        }

        if (ends(z, "\04" "ator"))

        {

            r(z, "\03" "ate");

            break;

        }

        break;

    case 's':

        if (ends(z, "\05" "alism"))

        {

            r(z, "\02" "al");

            break;

        }

        if (ends(z, "\07" "iveness"))

        {

            r(z, "\03" "ive");

            break;

        }

        if (ends(z, "\07" "fulness"))

        {

            r(z, "\03" "ful");

            break;

        }

        if (ends(z, "\07" "ousness"))

        {

            r(z, "\03" "ous");

            break;

        }

        break;

    case 't':

        if (ends(z, "\05" "aliti"))

        {

            r(z, "\02" "al");

            break;

        }

        if (ends(z, "\05" "iviti"))

        {

            r(z, "\03" "ive");

            break;

        }

        if (ends(z, "\06" "biliti"))

        {

            r(z, "\03" "ble");

            break;

        }

        break;

    case 'g':

        if (ends(z, "\04" "logi"))

        {

            r(z, "\03" "log");    /*-DEPARTURE-*/

            break;

        }



        /* To match the published algorithm, delete this line */



    }

}



/* step3(z) deals with -ic-, -full, -ness etc. similar strategy to step2. */



static void step3(struct stemmer * z)

{

    switch (z->b[z->k])

    {

    case 'e':

        if (ends(z, "\05" "icate"))

        {

            r(z, "\02" "ic");

            break;

        }

        if (ends(z, "\05" "ative"))

        {

            r(z, "\00" "");

            break;

        }

        if (ends(z, "\05" "alize"))

        {

            r(z, "\02" "al");

            break;

        }

        break;

    case 'i':

        if (ends(z, "\05" "iciti"))

        {

            r(z, "\02" "ic");

            break;

        }

        break;

    case 'l':

        if (ends(z, "\04" "ical"))

        {

            r(z, "\02" "ic");

            break;

        }

        if (ends(z, "\03" "ful"))

        {

            r(z, "\00" "");

            break;

        }

        break;

    case 's':

        if (ends(z, "\04" "ness"))

        {

            r(z, "\00" "");

            break;

        }

        break;

    }

}



/* step4(z) takes off -ant, -ence etc., in context <c>vcvc<v>. */



static void step4(struct stemmer * z)

{

    switch (z->b[z->k-1])

    {

    case 'a':

        if (ends(z, "\02" "al")) break;

        return;

    case 'c':

        if (ends(z, "\04" "ance")) break;

        if (ends(z, "\04" "ence")) break;

        return;

    case 'e':

        if (ends(z, "\02" "er")) break;

        return;

    case 'i':

        if (ends(z, "\02" "ic")) break;

        return;

    case 'l':

        if (ends(z, "\04" "able")) break;

        if (ends(z, "\04" "ible")) break;

        return;

    case 'n':

        if (ends(z, "\03" "ant")) break;

        if (ends(z, "\05" "ement")) break;

        if (ends(z, "\04" "ment")) break;

        if (ends(z, "\03" "ent")) break;

        return;

    case 'o':

        if (ends(z, "\03" "ion") && z->j >= 0 && (z->b[z->j] == 's' || z->b[z->j] == 't')) break;

        if (ends(z, "\02" "ou")) break;

        return;

    /* takes care of -ous */

    case 's':

        if (ends(z, "\03" "ism")) break;

        return;

    case 't':

        if (ends(z, "\03" "ate")) break;

        if (ends(z, "\03" "iti")) break;

        return;

    case 'u':

        if (ends(z, "\03" "ous")) break;

        return;

    case 'v':

        if (ends(z, "\03" "ive")) break;

        return;

    case 'z':

        if (ends(z, "\03" "ize")) break;

        return;

    default:

        return;

    }

    if (m(z) > 1) z->k = z->j;

}



/* step5(z) removes a final -e if m(z) > 1, and changes -ll to -l if

   m(z) > 1. */



static void step5(struct stemmer * z)

{

    char * b = z->b;

    z->j = z->k;

    if (b[z->k] == 'e')

    {

        int a = m(z);

        if (a > 1 || a == 1 && !cvc(z, z->k - 1)) z->k--;

    }

    if (b[z->k] == 'l' && doublec(z, z->k) && m(z) > 1) z->k--;

}



/* In stem(z, b, k), b is a char pointer, and the string to be stemmed is

   from b[0] to b[k] inclusive.  Possibly b[k+1] == '\0', but it is not

   important. The stemmer adjusts the characters b[0] ... b[k] and returns

   the new end-point of the string, k'. Stemming never increases word

   length, so 0 <= k' <= k.

*/



extern int stem(struct stemmer * z, char * b, int k)

{

    if (k <= 1) return k; /*-DEPARTURE-*/

    z->b = b;

    z->k = k; /* copy the parameters into z */



    /* With this line, strings of length 1 or 2 don't go through the

       stemming process, although no mention is made of this in the

       published algorithm. Remove the line to match the published

       algorithm. */



    step1ab(z);

    if (z->k > 0)

    {

        step1c(z);

        step2(z);

        step3(z);

        step4(z);

        step5(z);

    }

    return z->k;

}



/*--------------------stemmer definition ends here------------------------*/







static char * s;         /* buffer for words tobe stemmed */



#define INC 50           /* size units in which s is increased */

static int i_max = INC;  /* maximum offset in s */



#define LETTER(ch) (isupper(ch) || islower(ch))



void stemfile(struct stemmer * z, FILE * f,FILE *fout)

{

    //FILE *k;

    //k = fopen("After_Stemming.txt","a");

    while(TRUE)

    {

        int ch = getc(f);

        if (ch == EOF) return;

        if (LETTER(ch))

        {

            int i = 0;

            while(TRUE)

            {

                if (i == i_max)

                {

                    i_max += INC;

                    s = (char *)realloc(s, i_max + 1);

                }

                ch = tolower(ch); /* forces lower case */



                s[i] = ch;

                i++;

                ch = getc(f);

                if (!LETTER(ch))

                {

                    ungetc(ch,f);

                    break;

                }

            }

            s[stem(z, s, i - 1) + 1] = 0;

            /* the previous line calls the stemmer and uses its result to

               zero-terminate the string in s */

           // printf("%s",s);

            fprintf(fout,"%s\n",s);

        }

        else putchar(ch);

    }

}



/**********************STEMMER ENDS HERE *******************************************/







int Format(FILE *f1, FILE *f2)

//To format the words from a file--Remove punctuation & symbols ,Convert to lowercase,get all words in separate line

{

    char ch;

    do

    {

        ch = fgetc(f1);

        ch = tolower(ch); //Convert every character to Lowercase

        if (ch ==' ')     //To get every word in a new line we replace space by a new-line character

            ch = '\n';



        // if (ch == '"' ||ch == '.' ||ch == ',' ||ch == '!'|| ch == ':'||ch==';'||ch=='?'||ch=='\''||ch=='\\'||ch=='('||ch==')'||ch=='/'||ch=='+'||ch=='-'||ch=='*'||ch=='$')

        if(!((ch>='a' && ch<='z')||(ch>='A' && ch<='Z')||(ch=='\n'))) //We remove common grammatical symbols

            continue;

        fputc(ch, f2);

    }

    while (ch != EOF);



}



template<class Iter, class T>

Iter binary_find(Iter begin, Iter end, T val)

{

    // Finds the lower bound in at most log(last - first) + 1 comparisons

    Iter i = std::lower_bound(begin, end, val);// Returns an iterator pointing to the first element in the range [first,last) which does not compare less than val.



    if (i != end && !(val < *i))

        return i; // found

    else

        return end; // not found

}



int Format_Sent(FILE *f1,FILE *f2,FILE *fout)

{



    char current,prev;



    current=fgetc(f1);      //f1 points to the 2nd character

    fputc(current, fout);     // put the 1st character as it is in the output file

    do

    {

        current=fgetc(f1);      //current gets the 2nd character; f1 points to 3rd char

        prev=fgetc(f2);         //prev gets the 1st character; f2 points to 2rd char



        if(current == '.')      //case of '...' is skipped

        {

            if(prev>='A' && prev<='Z')  //previous is capital so must be acronym

                continue;

            else if(prev>='0' && prev<='9');

            else

            {

                current = '\n';

            }

        }

        if(current!=EOF)

        {

           // printf("%c",current);

            fputc(current, fout);

        }



    }

    while(current!=EOF);



}



int Sort()

{



    ifstream in("post_stemming.txt");          // Read names from file post Stemming (I/P file)

    if(!in.is_open())

        cout << "Unable to open stemmed file\n";



    ofstream sorted_file;

    sorted_file.open ("post_sorting.txt"); //O/P file



    ofstream stopword_filtered;

    stopword_filtered.open ("post_stopword_filtering.txt"); //Intermediate O/P file



    vector<string> names;



    string word;

    while(getline(in, word))

        names.push_back(word);



    sort(names.begin(), names.end());  //At this stage, names is a vector with sorted words (Not unique)



//     Frequency(names,filename);  //We calculate the frequency of each word before making implementing unique

    std::vector<string>::iterator temp;



    temp = std::unique (names.begin(), names.end());   //At this stage, names is a vector with sorted and UNIQUE words

    //Data about frequency of each word is lost



    names.resize( std::distance(names.begin(),temp) );  // Explicit Reduction in size after using std::unique

    for(temp=names.begin(); temp<names.end(); temp++)

        sorted_file <<*temp<< '\n';

    string stop_word;

    ifstream stop_file("Default_Stop_Words_After_Stemming.txt");



    //std::vector<string>::iterator temp;

    while(getline(stop_file,stop_word))    //Read a single string from stemmed stop words

    {

        //cout<<"STOP WORD :: "<<stop_word<<endl;



        temp =binary_find(names.begin(),names.end(),stop_word); //Using binary search since the vector is sorted

        if(temp != names.end() )    //Condition to check if the stop word is found in the sorted vector 'names'

        {



           // cout<<"MATCHED Stop Word :: "<<*temp<<endl;





            names.erase (temp);    //Remove the stop word from vector

            //  cout<<*temp<<endl;

        }



    }



    // Loop to print names

    for (size_t i = 0; i < names.size() - 1; i++)

    {

        MATRIX_COL++;   //Each word represents a column header

        stopword_filtered << names[i] << '\n';

    }

    stopword_filtered.close();

}



void PreStem(FILE *f2,FILE *fout)

{

    struct stemmer * z = create_stemmer();

    s = (char *) malloc(i_max + 1);



    stemfile(z, f2,fout);



    free(s);



    free_stemmer(z);



}

void ReadFile(FILE *in)

{

    /*************************FORMATTING**************************************/

    FILE *f1= NULL;   //intermediate pointer locating the file formed after formatting

    f1 = fopen("post_formatting.txt","w+");

    if(f1==NULL)

        cout<<"Could not open Formatting file"<<endl;

//   cout<<"test1"<<endl;

    Format(in,f1);

    fclose(f1);



    /***************************STEMMING****************************************/

//   cout<<"test2"<<endl;

    FILE *f2 = fopen("post_formatting.txt","r+");       //f2 is a pointer locating the file formed after formatting



    FILE *fout = fopen("post_stemming.txt","w+");

    if(fout==NULL)

        cout<<"Could not open stemming file"<<endl;



    PreStem(f2,fout);

    fclose(fout);

    fclose(f2);



    /**Sorting(Lexicographical Order) + StopWord Removal + Duplicate Words Removal**/

    /* 3 in 1 functionality

    1)Sorting

    2)Filtering to retain only Words

    3)Removing Stopwords */

    Sort();



}





void ReadWordFile(FILE *in,char filename[260])  //FUNCTION TO FORMAT AND STEM EACH SENTENCE

{

    /*************************FORMATTING**************************************/

    FILE *f1= NULL;   //intermediate pointer locating the file formed after formatting



    char add1[255]="./post_formatting/";    //Intermediate file address

    strcat(add1,filename);

    f1 = fopen(add1,"w+");

    if(f1==NULL)

        cout<<"Could not open Formatting file"<<endl;

    Format(in,f1);

    fclose(f1);



    /***************************STEMMING****************************************/

    FILE *f2 = fopen(add1,"r+");       //f2 is a pointer locating the file formed after stemming

    char add2[255]="./post_stemming/";    //Intermediate file address

    strcat(add2,filename);

    FILE *fout = fopen(add2,"w+");

    if(fout==NULL)

        cout<<"Could not open stemming file"<<endl;



    PreStem(f2,fout);

    fclose(fout);

    fclose(f2);



}

int Frequency(ifstream &file, char word[2000])         //Returns frequency of a word in a file

{



     int count=0;

     char ch[2000];



     while(file)

     {

      file>>ch;

      if(strcmp(ch,word)==0)

       count++;

     }

     file.close();

     return count;

   // return 0;

}

float PCC(int x[1000000],int y[1000000]) //Return the Pearson Coefficient Constant of two 1-D vectors (as arrays)

{

    int xy[MATRIX_COL], xsquare[MATRIX_COL], ysquare[MATRIX_COL];

    int i, n, xsum, ysum, xysum, xsqr_sum, ysqr_sum;

    float coeff, num, deno;



    xsum = ysum = xysum = xsqr_sum = ysqr_sum = 0;



    /*n is the number of entries*/

    n = MATRIX_COL;



    /* find the needed data to manipulate correlation coeff */

    for (i = 0; i < n; i++) {

            xy[i] = x[i] * y[i];

            xsquare[i] = x[i] * x[i];

            ysquare[i] = y[i] * y[i];

            xsum = xsum + x[i];

            ysum = ysum + y[i];

            xysum = xysum + xy[i];

            xsqr_sum = xsqr_sum + xsquare[i];

            ysqr_sum = ysqr_sum + ysquare[i];

    }



    num = 1.0 * ((n * xysum) - (xsum * ysum));

    deno = 1.0 * ((n * xsqr_sum - xsum * xsum)* (n * ysqr_sum - ysum * ysum));



    /* calculate correlation coefficient */

    coeff = num / sqrt(deno);

    return coeff;



}

float COSINE(int x[1000000],int y[1000000])

{

    double dot = 0.0, denom_a = 0.0, denom_b = 0.0 ;

    int Vector_Length=MATRIX_COL;

    for(unsigned int i = 0u; i < Vector_Length; ++i) {

        dot += x[i] * y[i] ;

        denom_a += x[i] * x[i] ;

        denom_b += y[i] * y[i] ;

    }

    return dot / (sqrt(denom_a) * sqrt(denom_b)) ;



}

void GetSummary(FILE *f_raw_file, FILE *output_file)
{

    FILE *f1,*f2,*f_intermediate,*fout;

    char ch,oneword[2000];

 //   f_raw_file = fopen("raw.txt","r");

    f_intermediate = fopen("input.txt","w");

    //reading words from raw.txt

    ch=fscanf(f_raw_file,"%s",oneword);

    while(ch!=EOF)

    {

        if(strcmp(oneword,"Mrs.")!=0 && strcmp(oneword,"Dr.")!=0 && strcmp(oneword,"Ms.")!=0 && strcmp(oneword,"Mr.")!=0)

        {

            fprintf(f_intermediate,"%s",oneword); //if word equal any of the salutation den don't print it in input.txt else print

            fprintf(f_intermediate,"%s"," ");

        }

        ch=fscanf(f_raw_file,"%s",oneword);

    }

    fclose(f_raw_file);

    fclose(f_intermediate);



    f1=fopen("input.txt","r+");

    f2=fopen("input.txt","r+");



    fout=fopen("sentence.txt","w");

    Format_Sent(f1,f2,fout);



    fclose(f2);

    fclose(f1);

    fclose(fout);



    /***********************CONVERT EACH SENTENCE INTO A SEPARATE TEXT FILE********************************/



    ifstream sen("sentence.txt");          // Read names from file post Stemming (I/P file)

    if(!sen.is_open())

        cout << "Unable to open sentence file\n";





    int position = 1;

    string word;

    while(getline(sen, word))

        /*Print the words of each sentence in a separate file numbered wrt position of sentence in original file*/



    {

        /*Convert integer variable 'position' to a string 'str'*/

        stringstream ss;

        ss << position;

        string str = ss.str();



        str+=".txt";    //Now 'str' is the name of the file where words of the sentence will be stored



        /*Convert String 'str' to char[] 'tab'*/

        char tab[1024];

        strncpy(tab, str.c_str(), sizeof(tab));

        tab[sizeof(tab) - 1] = 0;



        ofstream sentence_file;

        sentence_file.open (tab); //O/P file



        sentence_file << word <<endl;



        FILE *New_FILE = fopen(tab,"r+");

        ReadWordFile(New_FILE,tab);

        position++;

    }



    /**************TEXT TO SENTENCE CONVERSION COMPLETE :: RAW TEXT TO SORTED UNIQUE WORD CONVERSION STARTED****************/



    FILE *in;

    in = fopen("raw.txt","r+");

    ReadFile(in);

    fclose(in);

    /**************TEXT TO SORTED UNIQUE WORD CONVERSION COMPLETE :: PROCESS OF MAKING A MATRIX STARTED****************/

    /* A matrix has to drawn between words in post_stopword_filtering.txt as COLUMN HEADERS(MATRIX_COL)

     and sentences in sentence.txt as ROW HEADERS(MATRIX_ROW)*/



    MATRIX_ROW = position - 1; //assuming the creation of an extra file





    int matrix[MATRIX_ROW][MATRIX_COL];



    FILE *ffile;

    char cc,theword[100];

    ffile = fopen("post_stopword_filtering.txt","r+");

    //reading words from post_stopword_filtering.txt

    cc=fscanf(ffile,"%s",theword);

    int j=1;

    while(cc!=EOF)  //For every word to be searched

    {

        for(int i=1; i<=MATRIX_ROW; i++)    //For every sentence file

        {

            /*Convert integer variable 'position' to a string 'str'*/

               // cout<<"Value of MATRIX_ROW is == "<<i<<endl;



                stringstream p;

                p << i;

                string name = p.str();



                name+=".txt";    //Now 'str' is the name of the file where words of the sentence will be stored



                /*Convert String 'str' to char[] 'tab'*/

                char t[1024];

                strncpy(t, name.c_str(), sizeof(t));

                t[sizeof(t) - 1] = 0;       //Now 't' contains name of each sentence file. Eg; "1.txt"



                char addr[255]="./post_stemming/";    //Intermediate file address

                strcat(addr,t);



                //cout<<addr<<endl;





        /**NOW COUNTING THE FREQUENCY OF THE WORD IN EACH FILE**/



                 int count=0;

                 char current_word[100];

                 FILE *sen_file = fopen(addr,"r+");

                char cr;

                cr=fscanf(sen_file,"%s",current_word);

                while(cr!=EOF)

                {

                   //  cout<<"At this point, theword is :: "<<theword<<"and current word  is :: "<<current_word<<endl;

                    if(strcmp(theword,current_word)==0)

                        count++;

                    cr=fscanf(sen_file,"%s",current_word);

                }

                    fclose(sen_file);

                matrix[i-1][j-1] = count ;



        }

        cc=fscanf(ffile,"%s",theword);

        j++;            //Move to the next word

    }

    fclose(ffile);



   // cout<<"TEST"<<endl;





    ofstream mat;

    mat.open("matrix.txt");

    for(int l=0;l<MATRIX_ROW;l++)

    {

        for(int m=0;m<MATRIX_COL;m++)

        {



                mat<<matrix[l][m]<<" ";

        }

        mat<<endl;

    }

    /************************ NOW WE ARE MAKING COEFF MATRIX **************************************/

    float pcc_matrix[MATRIX_ROW][MATRIX_ROW];       // A square matrix of PCC values. PCC(i,j) is calculated over every two sentences

    float cosine_matrix[MATRIX_ROW][MATRIX_ROW];

    ofstream cof;

    cof.open("coefficient_matrix.txt");

    for(int m=0;m<MATRIX_ROW-1;m++)

    {

        for(int n=0;n<MATRIX_ROW-1;n++)

        {

                pcc_matrix[m][n] = PCC(matrix[m],matrix[n]);

                cof<<pcc_matrix[m][n]<<"    ";

        }

        cof<<"\n\n";

    }





    /******************AT THIS POINT WE HAVE A COEFFICIENT MATRIX******************/

    multimap< float, int > m; //Multimap will have the avg. of connectivity of a sentence with each of the other sentences.

    //'Key' of the multimap is the avg. PCC  and the 'value' represents the sentence position in sentence.txt

    for(int p=0;p<MATRIX_ROW-1;p++)

    {

        float sum=0.0;

         for(int q=0;q<MATRIX_ROW-1;q++)

        {

            sum+=pcc_matrix[p][q];

        }

         m.insert(pair<float,int>(sum,p));



       // cout<<m->first<<"  "<<m->second<<endl;

    }

    stack<int> S;

    for( multimap<float,int>::iterator ii=m.begin(); ii!=m.end(); ++ii)

    {

            //cout << (*ii).first << ": " << (*ii).second << endl;

            S.push((*ii).second);

            ct++;

    }



/************************ NOW Stack S contains position of sentences in decreasing order of avg. connectivity********************************/

    vector<string> str;



    ifstream temp("sentence.txt");

    string ss;

    while(getline(temp,ss))

    {

        str.push_back(ss);

    }

    temp.close();





    ofstream finale;

    finale.open("summary_by_algo1.txt");



    int h;

    cout<<"In how many sentences do you want the summary?\n"<<endl;

    cin>>h;

    while(h--)

    {

        finale<<str[S.top()]<<".";

        cout<<str[S.top()]<<".";

        rank1.push_back(S.top());

        S.pop();

    }

    finale.close();

   cout<<"\n\nWe are done with Algo1\nLet us See the summary by algoritm 2\n\n";



    /******************** ALGORITHM 2 STARTS HERE *******************************/

    FILE* myfile=NULL;

	float myvariable=0.0,high;

	int high_i=0,high_j=0,num_of_sentences;

	float **coeff;

	int i,k,loop=0;

	j=0;

	coeff= (float**)malloc(ct * sizeof(float *));

	for (i=0;i<ct;i++) //this loop allocates memory for rows n columns

	coeff[i]=(float*)malloc(ct * sizeof(float));

	myfile=fopen("coefficient_matrix.txt","r");

	for(i=0;i<ct;i++) //read the pcc values from file and storing it in the 2d array coeff

	{

		for (j=0;j<ct;j++)

		{

			fscanf(myfile,"%f",&myvariable);

			//printf("my variable %f\n",myvariable);

			(*(*(coeff + i)+ j))= myvariable;

		}

	}

	while(loop!=ct-1) //this loop will run till all the sentences are arranged in the dec order of pcc

	{

		high=-2;

		for(i=0;i<ct;i++)

		{



	      		for(j=0;j<ct;j++)        //comparing each value of array and finding out highest

			{

				if(high<*(*(coeff + i) + j) && *(*(coeff + i) + j)!=1 )

				{

					high=*(*(coeff + i) + j);

					high_i=i; //the indexes at which highest pcc exist are stored in high_i and high_j

					high_j=j;

				}

				else

					continue;

	        	}

		}

		enque(high_i,high_j); //indexes with highest pcc are pushed in the queue

		//nw decreasing the size of matriz by assigning -9 to unused places

		if(high_i<high_j)

		{

			for(j=1;j<ct;j++) //changing the pcc value and taking avg values ..

			{

				if(high_j==j)

					*(*(coeff + high_j)+ j)=1.0;

				else

				{

					*(*(coeff + high_j)+ j)=((*(*(coeff + high_i) + j))+(*(*(coeff + high_j) + j)))/2;

					*(*(coeff + j) + high_j)=*(*(coeff + high_j)+ j);

				}

			}

			for(j=0;j<ct;j++)

			{



				*(*(coeff+high_i)+j)=-9.0; //dec size of array by assigning -9 to the row n column which had highest pcc

				*(*(coeff+j)+high_i)=-9.0;

			}

		}

		else

		{

			for(j=1;j<ct;j++)

			{

				if(high_i==j)

					*(*(coeff + high_i) + j)=1.0;

				else

				{

					*(*(coeff + high_i) + j)=((*(*(coeff + high_i) + j))+(*(*(coeff + high_j) + j)))/2;

					*(*(coeff + j) + high_i)=*(*(coeff + high_i) + j);

				}

			}

			for(j=0;j<ct;j++)

			{

				*(*(coeff+high_j)+j)=-9.0;

				*(*(coeff+j)+high_j)=-9.0;

			}

		}

		loop++;

	}

	for(i=0;i<rear;i++)

	{

		for(j=i+1;j<rear;j++)  //now if some sentences in the sequence are repeated then assigning them to -1

		{

			if(sequence[i]==sequence[j])

				sequence[j]=-1;

		}

	}

	ofstream finale2;

    finale2.open("summary_by_algo2.txt");



	printf("\nIn how many sentences do you want the summary\n");

	scanf("%d",&num_of_sentences);

	printf("The order of sentences is\n");

	for(i=0;i<num_of_sentences;i++) //loop which will print the number of sentences asked by user ..

	{

		if(sequence[i]!=-1)

        {

            finale2 << str[sequence[i]] <<". ";

            cout << str[sequence[i]] <<". ";

            rank2.push_back(sequence[i]);

            //printf("%d\n",sequence[i]);

        }



		else

			num_of_sentences++;





	}

	fclose(myfile);

	cout<<"\n\nWe are done with Algo2\nLet us See the summary by algoritm 3\n\n";



/****************************** ALGORITHM 2 ENDS HERE  ********************************/



/************************************ALGORITHM 3 STARTS HERE **************************/



ofstream cos;

    cos.open("cosine_matrix.txt");

    for(int m=0;m<MATRIX_ROW-1;m++)

    {

        for(int n=0;n<MATRIX_ROW-1;n++)

        {

                cosine_matrix[m][n] = COSINE(matrix[m],matrix[n]);

                cos<<cosine_matrix[m][n]<<"    ";

        }

        cos<<"\n\n";

    }

    multimap< float, int > m1; //Multimap will have the avg. of connectivity of a sentence with each of the other sentences.

    //'Key' of the multimap is the avg. PCC  and the 'value' represents the sentence position in sentence.txt

    for(int p=0;p<MATRIX_ROW-1;p++)

    {

        float sum=0.0;

         for(int q=0;q<MATRIX_ROW-1;q++)

        {

            sum+=cosine_matrix[p][q];

        }

         m1.insert(pair<float,int>(sum,p));



       // cout<<m->first<<"  "<<m->second<<endl;

    }

    stack<int> S1;

    for( multimap<float,int>::iterator ii1=m1.begin(); ii1!=m1.end(); ++ii1)

    {

            //cout << (*ii).first << ": " << (*ii).second << endl;

            S1.push((*ii1).second);

    }



/************************ NOW Stack S contains position of sentences in decreasing order of avg. connectivity********************************/

    vector<string> str1;



   ifstream temp1("sentence.txt");

    string ss1;

    while(getline(temp1,ss1))

    {

        str1.push_back(ss1);

    }

    temp1.close();





    ofstream finale3;

    finale3.open("summary_by_algo3.txt");





    cout<<"\nIn how many sentences do you want the summary? "<<endl;

    cin>>h;

    while(h--)

    {

        finale3<<str1[S1.top()]<<".";

        cout<<str1[S1.top()]<<".";

        rank3.push_back(S1.top());

        S1.pop();

    }

    finale3.close();

   cout<<"\n\nWe are done with Algo3\nLet us See the summary by algoritm 4\n\n";









/*********************************ALGORITHM 4 STARTS HERE *********************************/





	FILE* newfile=NULL;

    myvariable=0.0,high=0.0;

	high_i=0,high_j=0,num_of_sentences=0;

	loop=0;

	j=0;

	rear=0;

	float **coef;

	//int i,k,loop=0;

	//j=0;

	coef= (float**)malloc(ct * sizeof(float *));

	for (i=0;i<ct;i++) //this loop allocates memory for rows n columns

	coef[i]=(float*)malloc(ct * sizeof(float));

	newfile=fopen("cosine_matrix.txt","r");

	//printf("%d\n",ct);

	for(i=0;i<ct;i++) //read the pcc values from file and storing it in the 2d array coeff

	{

		for (j=0;j<ct;j++)

		{

			fscanf(newfile,"%f",&myvariable);

			//printf("my variable %f\n",myvariable);

			(*(*(coef + i)+ j))= myvariable;

		}

	}

	while(loop!=ct-1) //this l9oop will run till all the sentences are arranged in the dec order of pcc

	{

		high=-2;

		for(i=0;i<ct;i++)

		{



	      		for(j=0;j<ct;j++)        //comparing each value of array and finding out highest

			{

				if(high<*(*(coef + i) + j) && *(*(coef + i) + j)!=1 )

				{

					high=*(*(coef + i) + j);

					high_i=i; //the indexes at which highest pcc exist are stored in high_i and high_j

					high_j=j;

				}

				else

					continue;

	        	}

		}

		enque_new(high_i,high_j); //indexes with highest pcc are pushed in the queue

		//nw decreasing the size of matriz by assigning -9 to unused places

		if(high_i<high_j)

		{

			for(j=1;j<ct;j++) //changing the pcc value and taking avg values ..

			{

				if(high_j==j)

					*(*(coef + high_j)+ j)=1.0;

				else

				{

					*(*(coef + high_j)+ j)=((*(*(coef + high_i) + j))+(*(*(coef + high_j) + j)))/2;

					*(*(coef + j) + high_j)=*(*(coef + high_j)+ j);

				}

			}

			for(j=0;j<ct;j++)

			{



				*(*(coef+high_i)+j)=-9.0; //dec size of array by assigning -9 to the row n column which had highest pcc

				*(*(coef+j)+high_i)=-9.0;

			}

		}

		else

		{

			for(j=1;j<ct;j++)

			{

				if(high_i==j)

					*(*(coef + high_i) + j)=1.0;

				else

				{

					*(*(coef + high_i) + j)=((*(*(coef + high_i) + j))+(*(*(coef + high_j) + j)))/2;

					*(*(coef + j) + high_i)=*(*(coef + high_i) + j);

				}

			}

			for(j=0;j<ct;j++)

			{

				*(*(coef+high_j)+j)=-9.0;

				*(*(coef+j)+high_j)=-9.0;

			}

		}

		loop++;

	}

//	for(i=0;i<rear;i++)

       // printf("%d\n",sequence1[i]);

	for(i=0;i<rear;i++)

	{

		for(j=i+1;j<rear;j++)  //now if some sentences in the sequence are repeated then assigning them to -1

		{

			if(sequence1[i]==sequence1[j])

				sequence1[j]=-1;

		}

	}

//	for(i=0;i<rear;i++)

  //      printf("%d\n",sequence1[i]);

	ofstream finale4;

    finale4.open("summary_by_algo4.txt");



	printf("\nIn how many sentences do you want the summary\n");

	scanf("%d",&num_of_sentences);

	printf("The order of sentences is\n");

	for(i=0;i<num_of_sentences;i++) //loop which will print the number of sentences asked by user ..

	{

		if(sequence1[i]!=-1)

        {

            finale4 << str[sequence1[i]] <<". ";

            cout << str[sequence1[i]] <<". ";

            rank4.push_back(sequence1[i]);

            //printf("%d\n",sequence[i]);

        }



		else

			num_of_sentences++;

    }

	fclose(newfile);

	cout<<"\n\nWe are done with Algo4\n\n";



    /****************************** ALGORITHM 4 ENDS HERE  ********************************/

    /******************************CONSENSUS STARTS HERE************************************/

    char l;

    cout<<"Do you want to get weighted rankings? Press 1 to confirm"<<endl;

    int fg;

    cin>>fg;

    double w1=1,w2=1,w3=1,w4=1;
    w1= 0.25;w2= 0.25;w3= 0.25;w4= 0.25;
/*
    if(fg == 1)

    {

        cout<<"Give weight for first algorithm"<<endl;

        cin>>w1;



        cout<<"Give weight for second algorithm"<<endl;

        cin>>w2;



        cout<<"Give weight for third algorithm"<<endl;

        cin>>w3;



        cout<<"Weight assigned to the fourth algorithm"<<endl;

        w4= (1 - (w1 + w2 + w3));

        cout<<w4;

    }
*/
    cout<<"Press a character to get rankings"<<endl;

    cin>>l;

    double points[100];

    for(int i=0;i<100;i++)

        points[i] = 0;

    cout<<"\n\n Ranking using 1st Algorithm \n"<<endl;

    int score = num_of_sentences*100;

    for(vector<int>::iterator j= rank1.begin(); j!=rank1.end();++j)

    {

        points[*j] += (score*w1);

        score-=100;

        cout<<*j<<endl;

    }

    cout<<"\n\n Ranking using 2nd Algorithm \n"<<endl;

    score = num_of_sentences*100;

    for(vector<int>::iterator j= rank2.begin(); j!=rank2.end();++j)

    {

        points[*j] += (score*w2);

        score-=100;

        cout<<*j<<endl;

    }

    cout<<"\n\n Ranking using 3rd Algorithm \n"<<endl;

    score = num_of_sentences*100;

    for(vector<int>::iterator j= rank3.begin(); j!=rank3.end();++j)

    {

        points[*j] += (score*w3);

        score-=100;

        cout<<*j<<endl;

    }

    cout<<"\n\n Ranking using 4th Algorithm \n"<<endl;

    score = num_of_sentences*100;

    for(vector<int>::iterator j= rank4.begin(); j!=rank4.end();++j)

    {

        points[*j] += (score*w4);

        score-=100;

        cout<<*j<<endl;

    }

    int max_index=0;

    cout<<"How many sentences do you want in the final summary?"<<endl;

    int num;

    cin>>num;

    for(int j=0;j<num;j++)

    {

        for(int i=0;i<100;i++)

         {

             if(points[max_index]< points[i])

             {

                max_index = i;

             }



         }

         cout << str[max_index] <<". ";

         string temp = str[max_index] + ". ";

        char *final=new char[temp.size()+1];
        final[temp.size()]=0;
        memcpy(final,temp.c_str(),temp.size());

         fprintf(output_file,"%s",final);
    }

    fclose(output_file);
    return ;

}

void enque(int i,int j)

{

	//printf(" in function %d %d\n",i,j);

	int flag1=0,flag2=0;

	sequence[rear]=i+1;

	rear++;

	sequence[rear]=j+1;

	rear++;



}

void enque_new(int i,int j)

{

	//printf(" in function %d %d\n",i,j);

	int flag1=0,flag2=0;

	sequence1[rear]=i+1;

	rear++;

	sequence1[rear]=j+1;

	rear++;



}
int main()
{
    for(int i=1; i<=30; i++)
    {

        stringstream ss;
        ss << i;
        string filename = ss.str();
        filename+=".txt";
        string input = "test\\source\\";
        string output = "test\\our_summary\\";
        input+=filename;
        output+=filename;

        //std::string s
        char *in=new char[input.size()+1];
        in[input.size()]=0;
        memcpy(in,input.c_str(),input.size());

        char *out=new char[output.size()+1];
        out[input.size()]=0;
        memcpy(out,output.c_str(),output.size());



        FILE *input_file = fopen(in,"r");

        FILE *output_file = fopen(out,"r");

       // ofstream summary;
       // summary.open(output);

        GetSummary(input_file,output_file);

    }
    return 0;
}
