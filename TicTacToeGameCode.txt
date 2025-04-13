#include <iostream>
#include <string>
#include <vector>
using namespace std;

namespace TicTacToe {
    char board[3][3];
    char currentPlayerSymbol;
    string player1, player2 = "AI";
    int score1 = 0, score2 = 0;
    bool vsAI = false;

    void initializeBoard() {
        currentPlayerSymbol = 'X';
        for (int i = 0; i < 3; ++i)
            for (int j = 0; j < 3; ++j)
                board[i][j] = '1' + i * 3 + j;
    }

    void printBoard() {
        cout << "\n";
        for (int i = 0; i < 3; ++i) {
            cout << " ";
            for (int j = 0; j < 3; ++j) {
                cout << board[i][j];
                if (j < 2) cout << " | ";
            }
            cout << "\n";
            if (i < 2) cout << "---+---+---\n";
        }
        cout << "\n";
    }

    void printScore() {
        cout << "SCORE:\n";
        cout << "  " << player1 << " (X): " << score1 << "\n";
        cout << "  " << player2 << " (O): " << score2 << "\n\n";
    }

    bool isValidMove(int pos) {
        int row = (pos - 1) / 3;
        int col = (pos - 1) % 3;
        return pos >= 1 && pos <= 9 && board[row][col] != 'X' && board[row][col] != 'O';
    }

    void makeMove(int pos) {
        int row = (pos - 1) / 3;
        int col = (pos - 1) % 3;
        board[row][col] = currentPlayerSymbol;
    }

    bool checkWin() {
        for (int i = 0; i < 3; ++i) {
            if ((board[i][0] == currentPlayerSymbol &&
                 board[i][1] == currentPlayerSymbol &&
                 board[i][2] == currentPlayerSymbol) ||
                (board[0][i] == currentPlayerSymbol &&
                 board[1][i] == currentPlayerSymbol &&
                 board[2][i] == currentPlayerSymbol))
                return true;
        }
        return ((board[0][0] == currentPlayerSymbol &&
                 board[1][1] == currentPlayerSymbol &&
                 board[2][2] == currentPlayerSymbol) ||
                (board[0][2] == currentPlayerSymbol &&
                 board[1][1] == currentPlayerSymbol &&
                 board[2][0] == currentPlayerSymbol));
    }

    bool isBoardFull() {
        for (int i = 0; i < 3; ++i)
            for (int j = 0; j < 3; ++j)
                if (board[i][j] != 'X' && board[i][j] != 'O')
                    return false;
        return true;
    }

    void switchPlayer() {
        currentPlayerSymbol = (currentPlayerSymbol == 'X') ? 'O' : 'X';
    }

    string getCurrentPlayerName() {
        return (currentPlayerSymbol == 'X') ? player1 : player2;
    }

    void updateScore() {
        if (currentPlayerSymbol == 'X') score1++;
        else score2++;
    }

    bool askToPlayAgain() {
        char choice;
        cout << "Do you want to play again? (y/n): ";
        cin >> choice;
        cin.ignore();
        return choice == 'y' || choice == 'Y';
    }

    int getWinningMove(char symbol) {
        for (int i = 1; i <= 9; ++i) {
            if (isValidMove(i)) {
                int row = (i - 1) / 3;
                int col = (i - 1) % 3;
                char temp = board[row][col];
                board[row][col] = symbol;
                bool win = checkWin();
                board[row][col] = temp;
                if (win) return i;
            }
        }
        return -1;
    }

    int getSmartMove() {
        // 1. Try to win
        int move = getWinningMove('O');
        if (move != -1) return move;

        // 2. Try to block player
        move = getWinningMove('X');
        if (move != -1) return move;

        // 3. Take center
        if (isValidMove(5)) return 5;

        // 4. Take corners
        vector<int> corners = {1, 3, 7, 9};
        for (int c : corners)
            if (isValidMove(c)) return c;

        // 5. First available
        for (int i = 1; i <= 9; ++i)
            if (isValidMove(i)) return i;

        return -1;
    }

    void aiMove() {
        int move = getSmartMove();
        if (move != -1) {
            cout << player2 << " (" << currentPlayerSymbol << ") chooses position " << move << ".\n";
            makeMove(move);
        }
    }

    void chooseGameMode() {
        int choice;
        cout << "Choose Game Mode:\n";
        cout << "  1. Player vs Player\n";
        cout << "  2. Player vs AI\n";
        cout << "Enter choice (1 or 2): ";
        cin >> choice;
        cin.ignore();
        vsAI = (choice == 2);

        if (vsAI) {
            cout << "Enter your name (you will be Player X): ";
            getline(cin, player1);
            player2 = "AI";
        } else {
            cout << "Enter name for Player 1 (X): ";
            getline(cin, player1);
            cout << "Enter name for Player 2 (O): ";
            getline(cin, player2);
        }
    }

    void playGame() {
        chooseGameMode();
        bool playing = true;

        while (playing) {
            initializeBoard();
            int move;

            while (true) {
                printBoard();

                if (vsAI && currentPlayerSymbol == 'O') {
                    aiMove();
                } else {
                    cout << getCurrentPlayerName() << " (" << currentPlayerSymbol << "), enter your move (1-9): ";
                    cin >> move;

                    if (!isValidMove(move)) {
                        cout << "Invalid move. Try again.\n";
                        continue;
                    }

                    makeMove(move);
                }

                if (checkWin()) {
                    printBoard();
                    cout << getCurrentPlayerName() << " (" << currentPlayerSymbol << ") wins!\n";
                    updateScore();
                    break;
                }

                if (isBoardFull()) {
                    printBoard();
                    cout << "It's a draw!\n";
                    break;
                }

                switchPlayer();
            }

            printScore();
            playing = askToPlayAgain();
        }

        cout << "Final Scores:\n";
        printScore();
        cout << "Thanks for playing, " << player1 << " and " << player2 << "!\n";
    }
}

int main() {
    cout << "Welcome to Tic-Tac-Toe!\n";
    TicTacToe::playGame();
    return 0;
}
