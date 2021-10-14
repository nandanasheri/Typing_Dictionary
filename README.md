# cs141---typing---dictionary
Program - Typing using Dictionary and Text file of commonly misspelled words. Timer program which checks against user's answers and generates score. CS 141, Fall 2021 at University of Illinois at Chicago
/* ----------------------------------------------------------------------
    Program 3: Typing
    Typing program with Timer and utilizing Dictionary and
    commonly misspelled words using the concept of random
    words from a text file.

    Course: CS 141, Fall 2021
    System: Windows using CLion
    Authors: Nandana Sheri 

   ----------------------------------------------------------------------
*/
#include <iostream>
#include <ctime>      // Needed to set the random number seed to be the current
#include <fstream>    // For file input and output
#include <vector>     // Input file is stored in a vector
#include <cassert>    // For the assert statement used to validate file open
#include <cctype>     // Allows using the tolower() function

using namespace std;  // So that we don't need to preface cin and cout with std::

// Function to show Menu Options of the program displayed here
void DisplayMenuOptions () {
    cout << "Select a menu option:\n"
            "   1. Spell commonly misspelled words (timed)\n"
            "   2. Type random words from full dictionary (timed)\n"
            "   3. Display some words from one of the lists\n"
            "   4. Use binary search to lookup a word in full dictionary\n"
            "   5. Set word length to use with full dictionary\n"
            "   6. Exit the program" << endl;
}

// Function used to read the Dictionary Text File into the Dictionary Vector
int ReadIntoDictionary (vector <string> &dictionary) {
    // Read in dictionary into dictionary vector
    ifstream inStream;                       // Declare an input stream for reading
    inStream.open( "dictionary.txt");     // Open dictionary file
    assert( inStream.fail() == false );      // Ensure file open worked
    dictionary.clear();                      // Clears the dictionary in case of any previous records
    // Keep repeating while input from the file yields a word
    string newWord;                          // Store a single input word
    while( inStream >> newWord) {            // While there is another word to be read
        dictionary.push_back( newWord);      // Add this new word to the end of the vector, growing it in the process
    }
    inStream.close();                               // Close the dictionary file
    const int wordsInDict = dictionary.size();      // To store the length of the dictionary
    cout << "There are " << wordsInDict << " words in the full dictionary." << endl;
    return wordsInDict;
}

// Function used to read the Commonly Misspelled Words Text File into the misspelledWords vector
// as well as the correct version of the commonly misspelled words into correctlySpelled vector
int ReadIntoSpelled (vector <string> &misspelledWords, vector <string> &correctlySpelledWords) {
    vector <string> temp;                          // To read all words into a temp file
    // Read in text file into temp vector
    ifstream inStream;                             // Declare an input stream for reading
    inStream.open( "misspelledWords.txt");      // Open dictionary file
    assert( inStream.fail() == false );            // Ensure file open worked
    temp.clear();                                  // Clears the vectors in case of any previous records
    misspelledWords.clear();
    correctlySpelledWords.clear();
    // Keep repeating while input from the file yields a word
    string newWord;                                // Store a single input word
    while( inStream >> newWord) {                  // While there is another word to be read
        temp.push_back( newWord);                  // Add this new word to the end of the vector, growing it in the process
    }
    for (unsigned int i = 0; i < temp.size(); i++) {
        if (i%2 == 0 or i%2 == 2) {                // All misspelled words will be read into this vector
            misspelledWords.push_back(temp.at(i));
        }
        else if (i % 2 == 1){                      // All correctly spelled words of the misspelled words will be read
            correctlySpelledWords.push_back(temp.at(i));
        }
    }
    inStream.close();                               // Close the dictionary file
    int wordsInMisspelled = misspelledWords.size(); // To store the number of commonly misspelled words
    cout << "There are " << wordsInMisspelled <<" commonly misspelled words." << endl;
    return wordsInMisspelled;
}

//Function to display a list of words for particular indices as entered by the user
void DisplayWords (vector <string> vectorInput) {
    cout << "Enter the start and end indices between 0 and " << vectorInput.size() - 1 << ":";
    int start,end,i;
    cin >> start;
    cin >> end;
    i = start;
    while (i <= end) {                                      // Prints the words from the vector
        cout << i << ". " << vectorInput.at(i) << endl;     // from the start value to the end
        i ++;
    }
}

// Function to display Menu Options for third choice and to take user's choice of function
void ListOfWords (vector <string> dictionary, vector <string> misspelledWords, vector <string> correctlySpelledWords) {
    char userChoice = ' ';
    cout << "Displaying word list entries.\n"
            "   Which words do you want to display?\n"
            "      A. Dictionary of all words\n"
            "      B. Wrongly spelled common misspelled words\n"
            "      C. Correctly spelled common misspelled words" << endl;
    cout << "Your choice -->";
    cin >> userChoice;
    if (userChoice == 'a'){
        DisplayWords(dictionary);
    }
    else if (userChoice == 'b') {
        DisplayWords(misspelledWords);
    }
    else if (userChoice == 'c') {
        DisplayWords(correctlySpelledWords);
    }
}

//Function to implement Binary Search to check whether a word is present in the Dictionary
int BinarySearch(string wordToSearch, vector <string> dictionary, int wordsInDict){
    int low,mid,high;
    low = 0;
    high = wordsInDict - 1;
    int i = 1;
    while (low <= high) {
        mid = (low + high) / 2;
        cout << i << ". Comparing to: " << dictionary.at(mid) << endl;
        i = i+1;                                            // To increase value of every comparison
        if (wordToSearch == dictionary.at(mid)) {           // If User finds the word in the dictionary
            return mid;                                     // Word is at the mid value
        }
        else if (wordToSearch < dictionary.at(mid)) {
            high = mid - 1;                                 // Word will be before the mid value
        }
        else if (wordToSearch > dictionary.at(mid)) {
            low = mid + 1;                                  // Word will be after the mid value
        }
    }
    return -1;                                              // If word was not found the function returns a -1
}

// Function to generate random words from a vector
void RandomWordsGenerator (vector <string> vectorInput, vector<string>  &randomWords, int wordLength, int menuOption) {
    randomWords.clear();                                // Used to clear randomWords vector if it holds anything
    if (menuOption == 1) {
        for (int i = 0; i < 5; i++) {
            int index = rand() % vectorInput.size();    // Generates random number within the limit of the vector
            string randWord = vectorInput.at(index);
            cout << randWord << " ";                    // Words of all lengths can be used
            randomWords.push_back(randWord);
        }
    }
    else if (menuOption == 2) {
        int i = 0;
        while (i < 5) {
            int index = rand() % vectorInput.size();
            string randWord = vectorInput.at(index);
            if (randWord.length() == wordLength) {              // To check whether length of random word
                cout << randWord << " ";                        // generated is the same as what we require
                randomWords.push_back(randWord);
                i++;
            }
            else {
                continue;
            }
        }
    }
    cout << endl;
}

// Function to check time with typing input
int TimeCheck (vector <string> randomWords, vector <string> &userWords) {
    userWords.clear();           // Used to clear userWords if it previously held anything
    int elapsedSeconds = 0;      // Will store number of elapsed seconds
    // Declare a variable to hold a time, and set it to the current time
    time_t startTime = time( NULL);
    for (int i = 0; i < 5; i++) {
        string userInput;
        cin >> userInput;
        userWords.push_back(userInput);
    }
    // Find the difference between the current time and the start time
    elapsedSeconds = difftime( time( NULL), startTime);
    return elapsedSeconds;
}

// Function to calculate the scores
int CalculateScore(int numOfCorrect, int numOfWrong, int elapsedSeconds, int menuOption, int wordLength) {
    int initialScore;
    int bonus;                              // Variables used to calculate bonus points as well as penalty points
    int penalty;                            // when time limit is passed or under
    int scoreCorrect;                       // Number of points for words typed correctly
    int scorePenalty;                       // Number of points for words typed incorrectly
    if (numOfWrong == 0) {                              // If all words are correctly spelled
        cout << "No misspelled words!" << endl;
    }
    cout << "Elapsed time: " << elapsedSeconds << " seconds. ";  // Returns the elapsed time in seconds
    if ( elapsedSeconds <= 15) {                                 // If user enters all words within the time limit
        int numberOfSecondUnder15 = 15 - elapsedSeconds;         // To calculate seconds under time limit
        bonus = numberOfSecondUnder15 * 2;                       // To calculate bonus within time limit
        cout << bonus << " point bonus for finishing " << numberOfSecondUnder15 << " seconds early." << endl;
    }
    else {                                                          // If user enters all words over the time limit
        int numberOfSecondsOver15 = elapsedSeconds - 15;            // To calculate seconds under time limit
        penalty = numberOfSecondsOver15 * 3;                        // To calculate penalty over time limit
        cout << "-" << penalty << " points for finishing " << numberOfSecondsOver15 << " seconds late." << endl;
    }
    if (menuOption == 1) {
        scoreCorrect = numOfCorrect * 3;                            // Score for Correctly Spelled Words
        scorePenalty = numOfWrong * 6;                              // Score for Incorrectly Spelled Words
    }
    else {
        scoreCorrect = numOfCorrect * (wordLength - 4);
        scorePenalty = numOfWrong * (wordLength - 4) * 2;
    }
    if (numOfCorrect >= 0) {
        if (menuOption == 1) {
            cout << scoreCorrect << " points: " << numOfCorrect << " spelled correctly x 3 points each" << endl;
        }
        else {
            cout << scoreCorrect << " points: " << numOfCorrect << " spelled correctly x "
            << (wordLength - 4) << " points each" << endl;
        }
    }
    if (numOfWrong > 0) {
        if (menuOption == 1) {
            cout << scorePenalty << " point penalty: " << numOfWrong << " spelled incorrectly x 6 points each" << endl;
        }
        else {
            cout << scorePenalty << " point penalty: " << numOfWrong << " spelled incorrectly x "
            << (wordLength - 4) * 2 << " points each" << endl;
        }
    }
    // To calculate total score depending on whether time limit is under or over
    if ( elapsedSeconds <= 15) {
        initialScore =  (bonus + scoreCorrect) - scorePenalty;
        cout <<  "Score: " << initialScore << endl;
    }
    else {
        initialScore = scoreCorrect - (scorePenalty + penalty);
        cout <<  "Score: " << initialScore << endl;
    }
    return initialScore;
}

// Function to check the User's answers and to count the number of correct and wrong answers respectively
int UserCheck (vector <string> randomWords, vector <string> correctlySpelledWords,
               vector <string> misspelledWords, vector <string> userWords,
               int wordLength, int elapsedSeconds, int menuOption) {
    int numOfCorrect = 0;                   // Variables declared to count the number of words the user
    int numOfWrong = 0;                     // entered correctly and incorrectly
    int z  = 1;                             // To place an if condition to print a statement only once in a loop
    // Checks through each word from list of random words to find a match of the word in the misspelled vector
    // when match is found the corresponding word the user typed is checked against the correctly spelled word
    for (int i = 0; i < randomWords.size(); i++ ) {
        for (int j = 0; j < correctlySpelledWords.size(); j ++){
            if (randomWords.at(i) == misspelledWords.at(j)) {
                if (userWords.at(i) == correctlySpelledWords.at(j)) {
                    numOfCorrect++;                     // Number of correct words increases by 1
                    break;                              // Exits loop after finding a match
                }
                else {
                    if ( z == 1) {
                        cout << "Misspelled words:" << endl;
                    }
                    z++;
                    cout << userWords.at (i) << " should be: " << correctlySpelledWords.at(j) << endl;
                    numOfWrong++;                       // Number of incorrect words increases by 1
                }
            }
        }
    }
    int score = CalculateScore(numOfCorrect, numOfWrong, elapsedSeconds, menuOption, wordLength);
    return score;
}

// Function to display Typing Program if user selects Menu Option 1
void TypingMisspelled (vector <string> misspelledWords, vector <string> correctlySpelledWords,
                       vector <string> &randomWords, int wordLength, vector <int> &scores, int menuOption) {
    vector <string> userWords;                              // To store the words the user types in
    int score = 0;                                          // Initializes score as 0
    if (menuOption == 1) {
        cout << "Using commonly misspelled words" << endl;
    }
    else if (menuOption == 2) {
        cout << "Using full dictionary" << endl;
    }
    RandomWordsGenerator(misspelledWords, randomWords, wordLength, menuOption);  // Generates random words
    cout << "Type in those words within goal of 15 seconds:" << endl;
    // Returns amount of time elapsed while accepting values from user
    int elapsedSeconds = TimeCheck(randomWords, userWords);
    // Checks the words the user typed with its correct spellings and returns score
    score = UserCheck (randomWords, correctlySpelledWords,
                           misspelledWords, userWords,
                           wordLength, elapsedSeconds, menuOption);
    scores.push_back(score);
}

// Function to return the best score from multiple attempts
void BestScore (vector <int> scores) {
    if (scores.empty()) {                       // In the case that the user never attempts to play the game
        cout << "Best score was 0";
    }
    else {
        int maxScore = scores.at(0);         // Initializes the first score in the vector as maxScore
        for (int i = 0; i < scores.size(); i++) {
            if (scores.at(i) > maxScore) {
                maxScore = scores.at(i);
            }
        }
        cout << "Best score was " << maxScore;
    }
}

//Main Function of the Program
int main() {
    // Declaration of vectors to store all words of the dictionary, commonly misspelled words and random words
    vector <string> dictionary, misspelledWords, correctlySpelledWords, randomWords;
    vector <int> scores;                                    // To store the scores of the user in multiple attempts
    int wordLength = 5;                                     // For Menu Option 2 initial length of random words is 5
    // Seed the random number generator so that the results are predictably the same.
    srand( 1);                                        // srand() makes the random number predictable
    int menuOption;
    // To read all words in text files into vector dictionary and misspelledWords,
    // print and return the size of each vector.
    int wordsInDict = ReadIntoDictionary(dictionary);
    int wordsInMisspelled = ReadIntoSpelled(misspelledWords, correctlySpelledWords);
    while (true) {
        DisplayMenuOptions();
        cout << "Your choice --> ";
        cin >> menuOption;
        // To spell commonly misspelled words within a particular time limit
        if (menuOption == 1) {
            TypingMisspelled(misspelledWords, correctlySpelledWords, randomWords,
                             wordLength, scores, 1);
        }
        // To type random words from the full dictionary
        else if (menuOption == 2) {
            TypingMisspelled(dictionary, dictionary, randomWords,
                             wordLength, scores, 2);
            wordLength++;
        }
        // To display a list of words for particular indices entered by user
        else if (menuOption == 3) {
            ListOfWords(dictionary, misspelledWords, correctlySpelledWords);
        }
        // To implement binary search to find a word entered by user
        else if (menuOption == 4) {
            string wordToSearch;
            cout << "Enter the word to lookup: ";     //Takes input value from the user for the word to be searched
            cin >> wordToSearch;
            int result = BinarySearch(wordToSearch, dictionary, wordsInDict);
            if (result == -1) {                       // Returns the default value if the word was not found
                cout << wordToSearch << " was NOT found." << endl;
            }
            else {
                cout << wordToSearch << " was found." << endl;
            }
        }
        // To change the length of random words generated when using the full dictionary
        // according to the user input
        else if (menuOption == 5) {
            cout << "Enter new wordLength:";
            cin >> wordLength;                        // New word length will now be stored into wordLength
        }
        // Handles for exiting program is user types 6
        else if (menuOption == 6) {
            cout << "Exiting program" << endl;
            BestScore(scores);
            break;
        }
    }
    return 0;
}
