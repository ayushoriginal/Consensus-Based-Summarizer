# Automatic-Text-Summarizer
## Author- Ayush Pareek
[Click here to see a powerpoint presentation of a primitive version of this project ](http://www.slideshare.net/ayushoriginal/abridged-project-pptayush)

[![Join the chat at https://gitter.im/Consensus-Based-Summarizer/Lobby](https://badges.gitter.im/Consensus-Based-Summarizer/Lobby.svg)](https://gitter.im/Consensus-Based-Summarizer/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
This is an implementation of "Automatic Consensus-Based Text Summarizer" along with text-organizing capabilities that can generate genre-specific, generic or user-configured  summaries of a large amount of unorganized text. We are currently using a number of independent text-mining algorithms based on different statistical models to compute the summaries and combining them using configurable consensus techniques. 

![0](http://i.imgur.com/OwsaI5v.jpg)

#How to Use

- **STEP 1::**Put the text you want to summarize in "raw.txt"(sample text is already provided). Compile using g++ 5.1 and execute.

- **STEP 2::**You will be asked to assign fraction ratio to each algorithm. Give that based on your requirements.

- **STEP 3::**You will be asked the number of lines in which you want in each summary. Give as any as you want.(based on Compression Ratio)
- **STEP 4::**O/P will be displayed on the screen as well as on "summary.txt".

## Requirements

- g++ 5.1 Compiler. (or maybe a few versions below would work as well)

# Frequently Asked Questions

## Q1. How to track the progress of the program to check its correctness? 
- CHECK 1: You will see each sentence being separated into its own text file numbered as  "position of sentence.txt". Eg; 5.txt refers to the text file containing only the 5th sentence of the original text

- CHECK 2: It has been made sure that pseudo full-stops (Eg; between acronyms like M.B.B.S. and after salutations like Mr. Mrs. Dr. Ms.) don't break the sentences. Put contents of 'raw2.txt' in 'raw.txt' to check the same

- CHECK 3: Various text files will be formed. Their description is provided::
    1)post_formatting.txt:: GIVES each document after removing symbols and numbers and converting  ' '(space)  to '\n'(next line) character. This is done by the Format() function in the code.
    2)post_stemming.txt:: Gives each document after passing it through the Portal stemmer.(SEE:: PreStem() and stemfile() function)
    3)post_sorting.txt:: After lexicographical  Sorting , only unique words are printed in the documents.  
    4)post_stopword_filtering.txt:: Sorted words without the stop words

- CHECK 4: post_stemming   &  post_formatting are two folders  which are not supposed to be deleted. They display each sentence file after doing the operation suggested by their names

- CHECK 5: "matrix.txt" shows a  WORD v/s SENTENCE MATRIX

 -CHECK 6: "coefficient_matrix.txt"  displays an  N*N matrix where each element i.e. c(i,j)  is the Pearson Correlation Coefficient of Sentence i and Sentence j. You can see that whenever (i == j) i.e. for diagonal elements ==> c(i,j) == 1

- CHECK 7:Finally the summary is displayed in summary.txt



#Paper accepted in The Thirteenth International Conference on Natural Language Processing 
##
![1](http://i.imgur.com/eZ8OwLC.jpg)
![2](http://i.imgur.com/ojMhoa9.jpg)
![3](http://i.imgur.com/H5LSmfn.jpg)
![4](http://i.imgur.com/d4bzKzi.jpg)
![5](http://i.imgur.com/YLsBsgw.jpg)
![6](http://i.imgur.com/qCJ5Xcb.jpg)
![7](http://i.imgur.com/nbJNoUz.jpg)
