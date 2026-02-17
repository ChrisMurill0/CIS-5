#include <iostream>
#include <iomanip>
#include <fstream>
#include <cstdlib>
#include <ctime>
#include <cstring>
#include <vector>
using namespace std;


const int MAXPLY = 5;
const int NMLEN = 15;
const int DECKSZ = 52;

//Function Prototypes
void shwMenu();
void shwRule(int = 21);                                    
void shwRule(char, int);                                   
void shflDck(int[], int);                                 
int  deal(int[], int&);                                    
int  hndVal(int[], int);                                   
bool playGm(int&, int&);                                   
void ldStats(char[][NMLEN], int[], int[], int&);           
void svStats(char[][NMLEN], int[], int[], int);            
void srtPlyr(char[][NMLEN], int[], int[], int);            
void bubSort(int[], int);                                  
int  fndPlyr(char[][NMLEN], int, char[]);                 
void shwLead(char[][NMLEN], int[], int[], int);            
void shwVec(vector<int>);                                  

//Program Execution
int main(int argc, char** argv) {
    srand(static_cast<unsigned>(time(0)));
    
    //Variables
    char pName[NMLEN];
    char names[MAXPLY][NMLEN];   
    int wins[MAXPLY];             
    int loss[MAXPLY];             
    int numPly = 0;
    int pWins = 0, pLoss = 0;
    int choice, pIdx;
    vector<int> scores;        
    
    //Load stats from file
    ldStats(names, wins, loss, numPly);
    
    cout << "============================" << endl;
    cout << "    WELCOME TO BLACKJACK" << endl;
    cout << "============================" << endl;
    cout << "Enter your name: ";
    cin.getline(pName, NMLEN);
    
    //Search for player
    pIdx = fndPlyr(names, numPly, pName);
    if (pIdx >= 0) {
        cout << "Welcome back " << pName << "!" << endl;
        pWins = wins[pIdx];
        pLoss = loss[pIdx];
    } else if (numPly < MAXPLY) {
        strcpy(names[numPly], pName);
        wins[numPly] = 0;
        loss[numPly] = 0;
        pIdx = numPly;
        numPly++;
    }
    
    //Main loop
    do {
        shwMenu();
        cin >> choice;
        cin.ignore();
        
        switch (choice) {
            case 1: {
                bool won = playGm(pWins, pLoss);
                wins[pIdx] = pWins;
                loss[pIdx] = pLoss;
                scores.push_back(won ? 1 : 0);  
                
                cout << (won ? "Nice win!" : "Better luck next time!") << endl;
                break;
            }
            case 2:
                cout << "\nYour Stats: " << pWins << " wins, ";
                cout << pLoss << " losses" << endl;
                if (scores.size() > 0) {
                    cout << "Session history: ";
                    shwVec(scores);
                }
                break;
            case 3:
                srtPlyr(names, wins, loss, numPly);
                shwLead(names, wins, loss, numPly);
                break;
            case 4:
                shwRule();      
                break;
            case 5:
                cout << "Saving and exiting..." << endl;
                svStats(names, wins, loss, numPly);
                exit(0);        
            default:
                cout << "Invalid choice!" << endl;
        }
    } while (choice != 5);
    
    //Save stats to file
    svStats(names, wins, loss, numPly);
    cout << "\nThanks for playing!" << endl;
    
    return 0;
}

//Display menu
void shwMenu() {
    cout << "\n1. Play Game" << endl;
    cout << "2. View Stats" << endl;
    cout << "3. Leaderboard" << endl;
    cout << "4. Rules" << endl;
    cout << "5. Exit" << endl;
    cout << "Choice: ";
}

//Show rules
void shwRule(int target) {
    cout << "\nGet close to " << target << " without going over." << endl;
    cout << "Ace=1 or 11, Face cards=10" << endl;
}

//Show rules
void shwRule(char c, int x) {
    cout << "\nBlackJack Rules - Target: " << x << endl;
}

//Shuffle deck
void shflDck(int deck[], int size) {
    for (int i = size - 1; i > 0; i--) {
        int j = rand() % (i + 1);
        int temp = deck[i];
        deck[i] = deck[j];
        deck[j] = temp;
    }
}

//Deal card
int deal(int deck[], int &idx) {
    int card = deck[idx];
    idx++;
    return card;
}

//Calculate hand value
int hndVal(int hand[], int size) {
    int total = 0, aces = 0;
    for (int i = 0; i < size; i++) {
        int card = hand[i] % 13 + 1;
        if (card == 1) {
            total += 11;
            aces++;
        } else if (card >= 10) {
            total += 10;
        } else {
            total += card;
        }
    }
    while (total > 21 && aces > 0) {
        total -= 10;
        aces--;
    }
    return total;
}

//Play one game
bool playGm(int &w, int &l) {
    static int gamesPlayed = 0;  
    gamesPlayed++;
    cout << "\nGame #" << gamesPlayed << endl;
    
    int deck[DECKSZ];
    int pHand[10], dHand[10];
    int pSz = 0, dSz = 0, dIdx = 0;
    char choice;
    
    //Initialize deck
    for (int i = 0; i < DECKSZ; i++) {
        deck[i] = i;
    }
    shflDck(deck, DECKSZ);
    
    //Deal cards
    pHand[pSz++] = deal(deck, dIdx);
    dHand[dSz++] = deal(deck, dIdx);
    pHand[pSz++] = deal(deck, dIdx);
    dHand[dSz++] = deal(deck, dIdx);
    
    cout << "Dealer shows: " << (dHand[0] % 13 + 1) << endl;
    cout << "Your total: " << hndVal(pHand, pSz) << endl;
    
    //Player turn
    bool bust = false;
    while (hndVal(pHand, pSz) < 21 && !bust) {
        cout << "Hit(H) or Stand(S)? ";
        cin >> choice;
        if (choice == 'H' || choice == 'h') {
            pHand[pSz++] = deal(deck, dIdx);
            cout << "Your total: " << hndVal(pHand, pSz) << endl;
            if (hndVal(pHand, pSz) > 21) {
                cout << "BUST! You lose." << endl;
                bust = true;
                l++;
                return false;  
            }
        } else {
            break;
        }
    }
    
    //Dealer turn
    int dTot = hndVal(dHand, dSz);
    while (dTot < 17) {
        dHand[dSz++] = deal(deck, dIdx);
        dTot = hndVal(dHand, dSz);
    }
    cout << "Dealer total: " << dTot << endl;
    
    int pTot = hndVal(pHand, pSz);
    if (dTot > 21 || pTot > dTot) {
        cout << "You WIN!" << endl;
        w++;
        return true;   
    } else if (pTot < dTot) {
        cout << "Dealer wins." << endl;
        l++;
        return false;  
    } else {
        cout << "Tie!" << endl;
        return false;  
    }
}

//Load from file
void ldStats(char names[][NMLEN], int wins[], int loss[], int &num) {
    ifstream fin("stats.dat");
    if (!fin) {
        num = 0;
        return;
    }
    num = 0;
    while (num < MAXPLY && fin >> names[num] >> wins[num] >> loss[num]) {
        num++;
    }
    fin.close();
}

//Save to file
void svStats(char names[][NMLEN], int wins[], int loss[], int num) {
    ofstream fout("stats.dat");
    for (int i = 0; i < num; i++) {
        fout << names[i] << " " << wins[i] << " " << loss[i] << endl;
    }
    fout.close();
}

//Sort by wins
void srtPlyr(char names[][NMLEN], int wins[], int loss[], int num) {
    for (int i = 0; i < num - 1; i++) {
        int max = i;
        for (int j = i + 1; j < num; j++) {
            if (wins[j] > wins[max]) {
                max = j;
            }
        }
        if (max != i) {
            //Swap names
            char tmp[NMLEN];
            strcpy(tmp, names[i]);
            strcpy(names[i], names[max]);
            strcpy(names[max], tmp);
            //Swap wins
            int t = wins[i];
            wins[i] = wins[max];
            wins[max] = t;
            //Swap losses
            t = loss[i];
            loss[i] = loss[max];
            loss[max] = t;
        }
    }
}

//Search for player
int fndPlyr(char names[][NMLEN], int num, char target[]) {
    for (int i = 0; i < num; i++) {
        if (strcmp(names[i], target) == 0) {
            return i;
        }
    }
    return -1;
}

//Display leaderboard
void shwLead(char names[][NMLEN], int wins[], int loss[], int num) {
    cout << endl;
    cout << setw(5) << "Rank" << setw(12) << "Name";
    cout << setw(8) << "Wins" << setw(8) << "Loss" << endl;
    cout << "---------------------------------" << endl;
    for (int i = 0; i < num; i++) {
        cout << setw(5) << (i + 1) << setw(12) << names[i];
        cout << setw(8) << wins[i] << setw(8) << loss[i] << endl;
    }
}

//Bubble Sort
void bubSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}

//Display vector
void shwVec(vector<int> v) {
    for (int i = 0; i < v.size(); i++) {
        cout << (v[i] == 1 ? "W " : "L ");
    }
    cout << endl;
}
