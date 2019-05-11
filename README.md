import java.util.Scanner;
import java.util.*;
import java.util.Random;
import java.util.List;
import java.util.ArrayList;


class Point {

    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public String toString() {
        return "[" + x + ", " + y + "]";
    }
}

class PointsAndScores {

    int score;
    Point point;

    PointsAndScores(int score, Point point) {
        this.score = score;
        this.point = point;
    }
}

class Board {
 
    public static final int NO_PLAYER = 0;
	public static final int PLAYER_X = 1;
	public static final int PLAYER_O = 2;
	private int[][] board = new int[3][3];
	public Point computerMove;

    public boolean isGameOver() {
        //Game is over is someone has won, or board is full (draw)
        return hasPlayerWon(PLAYER_X) || hasPlayerWon(PLAYER_O) || getAvailableCells().isEmpty();
    }

    public boolean hasPlayerWon(int player) {
        if ((board[0][0] == board[1][1] && board[0][0] == board[2][2] && board[0][0] == player) || (board[0][2] == board[1][1] && board[0][2] == board[2][0] && board[0][2] == player)) {
            //System.out.println("X Diagonal Win");
            return true;
        }
        for (int i = 0; i < 3; i++ ) {
            if ((board[i][0] == board[i][1] && board[i][0] == board[i][2] && board[i][0] == player )
                    || (board[0][i] == board[1][i] && board[0][i] == board[2][i] && board[0][i] == player)) {
                // System.out.println("X Row or Column win");
                return true;
            }
        }
        return false;
    }

   
    public List<Point> getAvailableCells() {
        List<Point> availableCells = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (board[i][j] == NO_PLAYER) {
                    availableCells.add(new Point(i, j));
                }
            }
        }
        return availableCells;
    }

    public boolean placeAMove(Point point, int player) {
        if (board[point.x][point.y] != NO_PLAYER)		
			return false;
		
		board[point.x][point.y] = player;
		return true;
    }
	public void  displayBoard() {
		System.out.println();
		
		for (int i=0; i<3; i++){
			for (int j = 0; j<3; j++){
				String value = "_";
				
				if (board[i][j] == PLAYER_X)
					value = "X";
				else if (board[i][j] == PLAYER_O)
					value = "O";
				System.out.print(value + " ");
			}
			System.out.println();
		}
		System.out.println();
	}
	public int minimax(int depth, int turn){
		if (hasPlayerWon(PLAYER_X))
			return 1;
		if (hasPlayerWon(PLAYER_O))
			return -1;
		
		List<Point> availableCells = getAvailableCells();
		
		if (availableCells.isEmpty())
			return 0;
		
		int min = Integer.MAX_VALUE;
		int max = Integer.MIN_VALUE;
		
		for (int i =0; i< availableCells.size(); i++){
			Point point = availableCells.get(i);
			
			if(turn == PLAYER_X){
				placeAMove(point, PLAYER_X);
				int currentScore = minimax(depth +1,PLAYER_O);
				max = Math.max(currentScore, max);
				
				if(depth == 0)
					System.out.println("Computer score for position" + point + " = " + currentScore);
				
				if(currentScore >=0)
					if(depth == 0)
						computerMove =point;
					
				if (currentScore == 1){
					board[point.x][point.y] = NO_PLAYER;
					break;
				}
				
				if( i == availableCells.size() - 1 && max <0)
					if (depth == 0)
						computerMove = point;
			}
			else if(turn == PLAYER_O){
				placeAMove(point,PLAYER_O);
				int currentScore = minimax(depth +1,PLAYER_X);
				min = Math.min(currentScore, min);
				
				if (min == -1){
					board[point.x][point.y] = NO_PLAYER;
					break;
				}
			}
			board[point.x][point.y] = NO_PLAYER;
		}
		return turn == PLAYER_X ? max:min;
	}
}
   

public class TicTacToe {
	
	public static final Random RANDOM = new Random();
	
    public static void main(String[] args) {
        Board b = new Board();
		Scanner scanner = new Scanner(System.in);
        b.displayBoard();

        System.out.println("Who's gonna move first? (1)Computer(X): (2)User(O): ");
        int choice = scanner.nextInt();
		
        if(choice == Board.PLAYER_X){
            Point p = new Point(RANDOM.nextInt(3), RANDOM.nextInt(3));
            b.placeAMove(p, Board.PLAYER_X);
            b.displayBoard();
        }
        
        while (!b.isGameOver()) {
			boolean moveOK = true;
			
			do{
				if(!moveOK){
					System.out.println("Cell already filled !");
				}
				System.out.println("Your move: ");
				Point userMove = new Point(scanner.nextInt(),scanner.nextInt());
				moveOK = b.placeAMove(userMove, Board.PLAYER_O);
			}
			while (!moveOK);
			
			b.displayBoard();
			
			if(b.isGameOver())
				break;
			
			b.minimax(0, Board.PLAYER_X);
			System.out.println("Computer choose position: " + b.computerMove);
			
			b.placeAMove(b.computerMove, Board.PLAYER_X);
			b.displayBoard();
            
        }
        if (b.hasPlayerWon(Board.PLAYER_X)) {
            System.out.println("You lost!");
        } else if (b.hasPlayerWon(Board.PLAYER_O)) {
            System.out.println("You win !");
        } else {
            System.out.println("Draw !");
        }
    }
}
