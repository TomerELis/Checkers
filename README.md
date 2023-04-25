#include <stdio.h>
#include <string.h>

/******************************************************************************

//Work of Tomer Elis 208677997 

*******************************************************************************/


int is_move_legal(char board[][BOARD_SIZE],char const CurrentPName[1], int p);    //check if the destaination is legal.
//As input gets the board, the current players signs as array and the index of player number(p).
//return the output as same as the function name play_single_turn.

int check_selection(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p); //check if the current selection is legal to play now.
//As input gets the board, the current players signs as array, the index of player number(p), and the row and col of the selection.
//return 1 for true and 0 for false.

void print_board(char board[][BOARD_SIZE]);     //print the current board status.get the board as input
int is_pawn_moveable(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p);  //check if the current location is movable.
// As input gets the board, the current players signs as array, the current location and the index of player number(p).
//return 1 for movable, 0 for not movable and other option to return from eating mode which resemble the direction of the eating.

int eating_mode(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p);   // Add this function to check if the pawn has options to eat
// As input gets the board, the current players signs as array, the current location and the index of player number(p).
//each return has an explanation in the function + there is a "map" at the end of the code to explain the return values.

int which_move(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p,int newRow, int newCol); // Add this function to check which move the pawn did
// As input gets the board, the current players signs as array, the current location+distinction and the index of player number(p).
//there is a "map" at the end of the code to explain the return values. 1000 represent illegal distination - kinda impossible to happened since we checked it before at is_movable.

int play_single_turn(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p, int newRow, int newCol, int checkMovable);
// As input gets the board, the current players signs as array, the current location+distinction, the index of player number(p) and a value which represent direction
// of a legal move. 1000 represent illegal distinction move.
//0 is a basic move 888 mean winning 1 is eating and 9 is bad destination.

int play(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p, int newRow, int newCol, int check);
//play rounds of the game using previous functions like play_single_turn and is_movable.
// As input gets the board, the current players signs as array, the current location, the index, play number(p) and a check value which represent
//check=1 means we made eating move. check = 0 is a basic move and check = 888 is winning move.
//return 1 if we want to continue play again and 0 if we want to end.

int end_game(char board[][BOARD_SIZE]);         //Ask the player if he wants to keep playing
//As input gets the board. return 1 if the player wants to keep playing and 0 if he wants to end the game.
void place_pawns(char board[][BOARD_SIZE]);     //rebuild the board to the original place of the pawns
//As input gets the board.
void print_Play_num(int i);                     //print the number of the current play
//As input gets an index of the current play.
void print_eating_feedback();                   //print the feedback of the eating move
void print_Draughts();                          //print the name of the game
void print_another_game();                      //print the feedback of the end of the game
void print_nowhere_to_move();                   //print the feedback of the pawn which has no legal moving options

int BOARD_SIZE=8;                   // Size of the board


int main() {
    setvbuf(stdout, NULL, _IONBF, 0);               //set debugger
    int row =0, col =0,p=1, check, i=1, GameOn =1;            //p is an index tell us which player is playing (1 = 'X' or 2 = 'O')
    char board[8][8], LowPlayer[2], HighPlayer[2], CurrentPName[3];         //LowPlayer is 'O' and HighPlayer is 'X'
    LowPlayer[1] = 'O', HighPlayer[1] = 'X';        //set players
    CurrentPName[1] = HighPlayer[1];
    CurrentPName[2] = LowPlayer[1];


    //building the board

    place_pawns(board);
    while(GameOn == 1) {
        print_Draughts();
        print_Play_num(i); i++; //print play number
        print_board(board);
        check = is_move_legal(board, CurrentPName, p);
        GameOn = play(board, CurrentPName, row, col, p, row, col, check);
        p = i = 1; //reset the index and play number
        place_pawns(board);

        //building board again for new game

    }

    return 0;
}
void print_board(char board[][BOARD_SIZE])
{
    int row,col;
    printf("  |0 1 2 3 4 5 6 7\n");
    printf("  *----------------\n");
    for (row = 0; row < 8; row++) {
        printf(" %d|", row);
        for (col = 0; col < 8; col++) {
            printf("%c ", board[row][col]);
        }
        printf("\n");
    }
    printf("\n");
}
int check_selection(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p){
    int check = 0;
    if(row < 0 || row > 7 || col < 0 || col > 7) { //check if the selection is in the array (board)
        return 0;
    }
    if (board[row][col] == CurrentPName[p]) {
        check = 1;
    }
    return check;
}
int is_move_legal(char board[][BOARD_SIZE], char const CurrentPName[1], int p) //Here the first move happening
{
    int row1, col1, row2, col2,checkMovable=0, ply, quit =0,flg=0;
    while (checkMovable == 0){
        if (flg==1){
            printf("Player %c - Bad pawn's selection please enter pawn's location again:\n", CurrentPName[p]);
        }
        else {
            printf("Player %c - Please enter pawn's location (row number followed by column number):\n",
                   CurrentPName[p]);
        }
        scanf("%d %d", &row1, &col1);
        flg=1;
        if (row1 ==999 || col1 ==999)
        {quit = 1;break;}
        while (check_selection(board, CurrentPName, row1, col1, p) == 0) {
            printf("Player %c - Bad pawn's selection please enter pawn's location again:\n", CurrentPName[p]);
            scanf("%d %d", &row1, &col1);
            if (row1 ==999 || col1 ==999)
            {quit = 1;break;}
        }
        if (quit == 1){return 999;}
        checkMovable = is_pawn_moveable(board,CurrentPName,row1,col1,p);
        if (checkMovable == 0){
            print_nowhere_to_move();
        }}
    if (quit == 1){return 999;}
    int j=9, flg2=0;
    while (j==9){
        if (flg2==1){
            printf("Player %c - Bad pawn's destination please enter pawn's destination again:\n",CurrentPName[p]);
        }
        else{
            printf("Player %c - Please enter pawn's destination (row number followed by column number):\n", CurrentPName[p]);
            flg2=1;}
        scanf("%d %d", &row2, &col2);
        checkMovable = which_move(board,CurrentPName,row1,col1,p,row2,col2);
        ply = play_single_turn(board, CurrentPName, row1, col1, p, row2, col2, checkMovable);
        j= ply;
    }
    return j;

}
int is_pawn_moveable(char board[][BOARD_SIZE], char const CurrentPName[2], int row, int col, int p)
{
    int check = 0;
    if (CurrentPName[p] == CurrentPName[1]) {
        //check if I am pawn X and has basic move option
        if ((board[row + 1][col + 1] == '.' )|| board[row + 1][col - 1] == '.') {
            if (board[row + 1][col + 1] == '.' ) {
                return 1;
            }
            if (board[row + 1][col - 1] == '.') {
                return 1;
            }
        }
        //check if I am pawn X and has eating option
        check = eating_mode(board,CurrentPName,row,col,p);
        if (check != 0){
            return check;
        }
    }

    else if (CurrentPName[p] == CurrentPName[2]) {
        //check if I am pawn O and has basic move option
        if (board[row - 1][col + 1] == '.' || board[row - 1][col - 1] == '.') {
            if (board[row - 1][col + 1] == '.') {
                return 1;
            }
            if (board[row - 1][col - 1] == '.') {
                return 1;
            }
        }

        //check if I am pawn O and has eating option
        check = eating_mode(board,CurrentPName,row,col,p);
        if (check != 0){
            return check;
        }

    }

    return 0;
}
int eating_mode(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p){
    if (CurrentPName[p] == CurrentPName[1]) {
        //TODO - not sure if its necessary to check "row<7" cause I check char "." either way.

        if (board[row+2][col+2] == '.' && board[row+1][col+1] == 'O' && ((row<7) && (col<7))){
            return 22;     //mean we are 'X' and eating diagonally to the right
        }
        if (board[row+2][col-2] == '.' && board[row+1][col-1] == 'O' && ((row<7) && (col>0))){
            return 28;    //mean we are 'X' and eating diagonally to the left

            //meaning of the numbers returned are at the end of the code

        }
    }
    else if (CurrentPName[p] == CurrentPName[2]) {
        if (board[row -2][col +2] == '.' && board[row -1][col +1] == 'X' && ((row<7) && (col<7))) {
            return 82;   //mean we are 'O' and eating diagonally to the right
        }
        else if (board[row -2][col -2] == '.' && board[row -1][col -1] == 'X' && ((row<7) && (col>0))) {
            return 88;  //mean we are 'O' and eating diagonally to the left
        }
    }


    return 0;
}
int play_single_turn(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p, int newRow, int newCol, int checkMovable)
{
    if ((checkMovable == 11) && (newRow == row +1) && (newCol == col +1)){
        board[newRow][newCol] = CurrentPName[p];
        board[row][col] = '.';if(row+1==7){return 888;}
        return 0;}
    if ((checkMovable == 19) && (newRow == row +1) && (newCol == col -1)){
        board[newRow][newCol] = CurrentPName[p];
        board[row][col] = '.'; if(row+1==7){return 888;}
        return 0;}
    else if ((checkMovable == 91) && (newRow == row -1) && (newCol == col +1)){
        board[newRow][newCol] = CurrentPName[p];
        board[row][col] = '.';if(row-1==0){return 888;}
        return 0;}
    else if ((checkMovable == 99) && (newRow == row -1) && (newCol == col -1)){
        board[newRow][newCol] = CurrentPName[p];
        board[row][col] = '.';if(row-1==0){return 888;}
        return 0;}
////////// Eating award with 1 point, therefore we'll return 1 and not 0 when eating occurred . 888 means winning//////////
    if ((checkMovable == 22) && (newRow == row+2) && (newCol == col+2)){
        board[newRow][newCol] = CurrentPName[p];
        board[row+1][col+1] = '.';
        board[row][col] = '.'; if(row+2==7){return 888;}
        return 1;}
    else if ((checkMovable == 28) && (newRow == row+2) && (newCol == col-2)){
        board[newRow][newCol] = CurrentPName[p];
        board[row+1][col-1] = '.';
        board[row][col] = '.'; if(row+2==7){return 888;}
        return 1;}
    else if ((checkMovable == 82) && (newRow == row -2) && (newCol == col +2)){
        board[newRow][newCol] = CurrentPName[p];
        board[row-1][col+1] = '.';
        board[row][col] = '.'; if(row-2==0){return 888;}
        return 1;}
    else if ((checkMovable == 88) && (newRow == row -2) && (newCol == col -2)){
        board[newRow][newCol] = CurrentPName[p];
        board[row-1][col-1] = '.';
        board[row][col] = '.'; if(row-2==0){return 888;}
        return 1;}
    return 9;}
int which_move(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p,int newRow, int newCol){

    if ((CurrentPName[p] == CurrentPName[1]) && (newRow == row +1) && (newCol == col +1) && (board[newRow][newCol] == '.')){
        return 11;
    }
    else if ((CurrentPName[p] == CurrentPName[1]) && (newRow == row +1) && (newCol == col -1) && (board[newRow][newCol] == '.')){
        return 19;
    }
    else if ((CurrentPName[p] == CurrentPName[2] && (newRow == row -1) && (newCol == col +1)) && (board[newRow][newCol] == '.')){
        return 91;
    }
    else if ((CurrentPName[p] == CurrentPName[2] && (newRow == row -1) && (newCol == col -1)) && (board[newRow][newCol] == '.')){
        return 99;
    }


    else if ((CurrentPName[p] == CurrentPName[1]) && (newRow == row+2) && (newCol == col+2) && (board[row+1][col+1] == CurrentPName[(p%2)+1])){
        return 22;
    }
    else if ((CurrentPName[p] == CurrentPName[1]) && (newRow == row+2) && (newCol == col-2) && (board[row+1][col-1] == CurrentPName[(p%2)+1])){
        return 28;
    }
    else if ((CurrentPName[p] == CurrentPName[2] && (newRow == row -2) && (newCol == col +2)) && (board[row-1][col+1] == CurrentPName[(p%2)+1])){
        return 82;
    }
    else if ((CurrentPName[p] == CurrentPName[2] && (newRow == row -2) && (newCol == col -2)) && (board[row-1][col-1] == CurrentPName[(p%2)+1])){
        return 88;
    }
    return 1000;
}
int play(char board[][BOARD_SIZE], char const CurrentPName[1], int row, int col, int p, int newRow, int newCol, int check){
    int i=2,PlayAgain=100,Xscore=0,Oscore=0;          //most game moves happened here
    while (1){
        if (check == 888){
            printf("Wooohooo! player %c Won!\n", CurrentPName[p]);
            PlayAgain = end_game(board);
            if (PlayAgain == 1) {
                return 1;
            }
            else if (PlayAgain == 0) {
                return 0;
            }
        }
        else if (check == 999){
            printf("Player %c decided to quit!\n", CurrentPName[p]);
            PlayAgain = end_game(board);
            if (PlayAgain == 1) {
                return 1;
            }
            else if (PlayAgain == 0) {
                return 0;
            }
        }
        if (check ==1){
            print_eating_feedback();}
        printf("X score:%d       O score:%d", Xscore, Oscore);
        print_Play_num(i); i++; //print play number
        print_board(board);
        p = (p % 2) + 1;
        check = is_move_legal(board, CurrentPName, p);
        if (check ==1){
            if (p ==1){
                Xscore = Xscore+1;
                if (Xscore == 12){check = 888;
                    print_eating_feedback();}
            }
            else{
                Oscore = Oscore+1;
                if (Oscore == 12){check = 888;
                    print_eating_feedback();}
            }}

    }}
int end_game(char board[][BOARD_SIZE]){ //Ask the player if he wants to keep playing
    int flg=0;
    char playAgain[1];
    print_board(board);
    while(flg ==0){
        while(getchar()!='\n');
        print_another_game();
        scanf("%c", playAgain);
        if (playAgain[0] == 'y'){
            flg = 1;
            return 1;
        }
        else if (playAgain[0] == 'n'){
            flg = 1;
            return 0;
        }
        else{
        }
    }

}
void place_pawns(char board[][BOARD_SIZE]) {
    int row = 0, col = 0;
    char LowPlayer[2], HighPlayer[2], CurrentPName[3];         //LowPlayer is 'O' and HighPlayer is 'X'
    LowPlayer[1] = 'O', HighPlayer[1] = 'X';        //set players
    for (row = 0; row < 8; row++) {
        for (col = 0; col < 8; col++) {
            if ((row + col) % 2 == 1) {
                if (row > 4) { board[row][col] = LowPlayer[1]; }
                else if (row < 3) { board[row][col] = HighPlayer[1]; }
                else { board[row][col] = '.'; }
            }
            else { board[row][col] = ' '; }
        }
    }
}
void print_Play_num(int i)  {                   //print the number of the current play
    printf("\n----------Play number:%d----------\n\n", i);
}
void print_eating_feedback(){                   //print the feedback of the eating move
    printf("Ho nice move! you got him... \n");}
void print_Draughts() {
    printf("Lets play Draughts!\n");
}
void print_another_game()
{
    setbuf(stdin, NULL);
    printf("Would you like to play another game?(y\\n)\n");
}
void print_nowhere_to_move()
{
    printf ("This pawn has nowhere to move!\n");
}




//MAP OF THE BOARD
//          '9' equal ← or ↓ and '1' equal → or ↑           depends on the position (first entering row)
//          '8' equal ←← or ↓↓ and '2' equal →→ or ↑↑       depends on the position (first entering row)
// 8 and 9 are like "minus" or "double minus" and 1 and 2 are like "plus" or "double plus"
//the first number tell me the left or right and how many. the second number tell me the up or down and how many.
//example : player insert 81 means he wants to move twice to the left and one time up. ←← and ↑
//example : player insert 99 means he wants to move one to the left and one time down. ← and ↓
//example : player insert 21 means he wants to move twice to the right and one time up. →→ and ↑
