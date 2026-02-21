# chess-game-c
Desenvolvendo a Lógica do Jogo 

git clone https://github.com/seu-usuario/chess-game-c.git
cd chess-game-c

mkdir src
# cole main.c em src/
# adicione .gitignore e README.md
git add .
git commit -m "Versão inicial do jogo de xadrez em C"
git push origin main

chess-game-c/
├── README.md
├── src/
│   └── main.c
└── .gitignore

#include <stdio.h>
#include <ctype.h>

#define SIZE 8

typedef enum {EMPTY, PAWN, ROOK, KNIGHT, BISHOP, QUEEN, KING} PieceType;
typedef enum {NONE, WHITE, BLACK} Color;

typedef struct {
    PieceType type;
    Color color;
} Piece;

typedef struct {
    int x;
    int y;
} Position;

void initBoard(Piece board[SIZE][SIZE]) {
    // Inicializa tabuleiro vazio
    for(int i=0;i<SIZE;i++)
        for(int j=0;j<SIZE;j++)
            board[i][j] = (Piece){EMPTY, NONE};

    // Peças brancas
    board[0][0] = board[0][7] = (Piece){ROOK, WHITE};
    board[0][1] = board[0][6] = (Piece){KNIGHT, WHITE};
    board[0][2] = board[0][5] = (Piece){BISHOP, WHITE};
    board[0][3] = (Piece){QUEEN, WHITE};
    board[0][4] = (Piece){KING, WHITE};
    for(int i=0;i<SIZE;i++)
        board[1][i] = (Piece){PAWN, WHITE};

    // Peças pretas
    board[7][0] = board[7][7] = (Piece){ROOK, BLACK};
    board[7][1] = board[7][6] = (Piece){KNIGHT, BLACK};
    board[7][2] = board[7][5] = (Piece){BISHOP, BLACK};
    board[7][3] = (Piece){QUEEN, BLACK};
    board[7][4] = (Piece){KING, BLACK};
    for(int i=0;i<SIZE;i++)
        board[6][i] = (Piece){PAWN, BLACK};
}

void printPiece(Piece p) {
    char c = ' ';
    switch(p.type) {
        case PAWN: c = 'P'; break;
        case ROOK: c = 'R'; break;
        case KNIGHT: c = 'N'; break;
        case BISHOP: c = 'B'; break;
        case QUEEN: c = 'Q'; break;
        case KING: c = 'K'; break;
        default: c = '.'; break;
    }
    if(p.color == BLACK && p.type != EMPTY) c += 32; // minúscula para pretos
    printf("%c ", c);
}

void printBoard(Piece board[SIZE][SIZE]) {
    for(int i=SIZE-1;i>=0;i--){
        printf("%d ", i+1);
        for(int j=0;j<SIZE;j++)
            printPiece(board[i][j]);
        printf("\n");
    }
    printf("  a b c d e f g h\n");
}

int isOnBoard(Position p) {
    return p.x >=0 && p.x < SIZE && p.y >=0 && p.y < SIZE;
}

int validMove(Piece board[SIZE][SIZE], Position from, Position to) {
    if(!isOnBoard(from) || !isOnBoard(to)) return 0;
    Piece p = board[from.x][from.y];
    Piece dest = board[to.x][to.y];

    if(p.type == EMPTY) return 0;
    if(dest.color == p.color) return 0;

    int dx = to.x - from.x;
    int dy = to.y - from.y;

    switch(p.type){
        case PAWN:
            if(p.color == WHITE && dx == 1 && dy == 0 && dest.type == EMPTY) return 1;
            if(p.color == BLACK && dx == -1 && dy == 0 && dest.type == EMPTY) return 1;
            if(p.color == WHITE && dx == 1 && abs(dy)==1 && dest.color == BLACK) return 1;
            if(p.color == BLACK && dx == -1 && abs(dy)==1 && dest.color == WHITE) return 1;
            return 0;
        case ROOK:
            if(dx==0 || dy==0) return 1;
            return 0;
        case KNIGHT:
            if((abs(dx)==2 && abs(dy)==1) || (abs(dx)==1 && abs(dy)==2)) return 1;
            return 0;
        case BISHOP:
            if(abs(dx)==abs(dy)) return 1;
            return 0;
        case QUEEN:
            if(dx==0 || dy==0 || abs(dx)==abs(dy)) return 1;
            return 0;
        case KING:
            if(abs(dx)<=1 && abs(dy)<=1) return 1;
            return 0;
        default:
            return 0;
    }
}

int main() {
    Piece board[SIZE][SIZE];
    initBoard(board);

    Color turn = WHITE;
    char fromCol, toCol;
    int fromRow, toRow;

    while(1) {
        printBoard(board);
        printf("%s move (ex: e2 e4): ", turn == WHITE ? "White" : "Black");
        scanf(" %c%d %c%d", &fromCol, &fromRow, &toCol, &toRow);

        Position from = {fromRow-1, fromCol-'a'};
        Position to = {toRow-1, toCol-'a'};

        if(validMove(board, from, to)){
            board[to.x][to.y] = board[from.x][from.y];
            board[from.x][from.y] = (Piece){EMPTY, NONE};
            turn = turn == WHITE ? BLACK : WHITE;
        } else {
            printf("Movimento inválido!\n");
        }
    }
    return 0;
}
