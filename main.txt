
import java.util.ArrayList;
import javafx.application.Application;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.StackPane;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;
import javafx.scene.input.MouseEvent;
import javafx.animation.PauseTransition;
import javafx.util.Duration;

public class Main extends Application {
    private int clickCounter = 0;//==64 end game
    private int[][] board = new int[8][8];//==layout chess bord
    private int flag1, flag2;
    private int clicking = 0;// find 5 consequtive bricks
    private Label [][]autoBoard;

    @Override
    public void start(Stage primaryStage) {
        Stage mainPage = new Stage();

        Button b1 = new Button("Two Players");
        b1.setMinSize(50, 30);
        Button b2 = new Button("Player 1");
        b2.setMinSize(50, 30);
        Button b3 = new Button("Player 2");
        b2.setMinSize(50, 30);

        VBox vb = new VBox();
        vb.setSpacing(12);
        vb.getChildren().addAll(b1, b2, b3);
        vb.setStyle("-fx-background-color: white; -fx-text-fill: white;");
        vb.setAlignment(Pos.CENTER);
        Scene mainScene = new Scene(vb, 300, 300);
        mainPage.setScene(mainScene);
        mainPage.show();
        mainPage.setTitle("Magnetic Cave");

        b1.setOnAction(e -> {
            board = new int [8][8];
            primaryStage.setTitle("Two Players");

            GridPane boardPane = createBoardPane();

            Scene scene = new Scene(boardPane, 450, 450);
            primaryStage.setScene(scene);
            primaryStage.show();
        });

        b2.setOnAction(e -> {
            board = new int [8][8];
            primaryStage.setTitle("Player 1");
            flag1 = 10;

            GridPane gridPane = createGridPane();
            gridPane.setAlignment(Pos.CENTER);

            //Scene scene = new Scene(boardPane, 450, 450);
            Scene scene = new Scene(gridPane, 450, 450);
            primaryStage.setScene(scene);
            primaryStage.show();
        });

        b3.setOnAction(e -> {
            board = new int [8][8];
            primaryStage.setTitle("Player 2");
            flag2 = 10;

            GridPane gridPane = createGridPane();
            gridPane.setAlignment(Pos.CENTER);

            //Scene scene = new Scene(boardPane, 450, 450);
            Scene scene = new Scene(gridPane, 450, 450);
            primaryStage.setScene(scene);
            primaryStage.show();
        });
    }

    private GridPane createBoardPane() {
        GridPane boardPane = new GridPane();
        boardPane.setAlignment(Pos.CENTER);
        boardPane.setHgap(1);
        boardPane.setVgap(1);

        boolean isWhiteSquare = true;
        for (int row = 0; row < 8; row++) {
            isWhiteSquare = !isWhiteSquare;
            for (int col = 0; col < 8; col++) {
                StackPane squarePane = createSquarePane(isWhiteSquare, row, col);
                boardPane.add(squarePane, col, row);
                isWhiteSquare = !isWhiteSquare;
            }
        }

        return boardPane;
    }

    private StackPane createSquarePane(boolean isWhiteSquare, int row, int col) {
        StackPane squarePane = new StackPane();
        squarePane.setMinSize(50, 50);
        squarePane.setStyle(isWhiteSquare ? "-fx-background-color: white;" : "-fx-background-color: gray;");

        squarePane.setOnMouseClicked(e -> {
            handleSquareClick(squarePane, row, col);
            clicking++;

        });
        //	minmax(board, 2, isWhiteSquare);
        if (flag1 == 10 && clicking % 2 != 0) {
            //	minmax(board, 2, isWhiteSquare);
        }
        if (flag2 == 10) {
            handleSquareClick(squarePane, row, col);
        }

        return squarePane;
    }

    private void handleSquareClick(StackPane squarePane, int row, int col) {
        if (isMoveAvailable(row, col)) {
            clickCounter++;
            if (clickCounter % 2 == 1) {
                squarePane.getChildren().add(createLabel("■"));
                board[row][col] = 1;
                boolean checker = checkWinner(board, 1);
                if (checker) {
                    Alert alert = new Alert(Alert.AlertType.INFORMATION);
                    alert.setTitle("Player 1 wins!");
                    alert.setHeaderText(null);
                    alert.setContentText("Player 1 wins!");
                    alert.showAndWait();
                    resetGame();
                }
            } else {
                squarePane.getChildren().add(createLabel("□"));
                board[row][col] = 2;
                boolean checker = checkWinner(board, 2);
                if (checker) {
                    Alert alert = new Alert(Alert.AlertType.INFORMATION);
                    alert.setTitle("Player 2 wins!");
                    alert.setHeaderText(null);
                    alert.setContentText("Player 2 wins!");
                    alert.showAndWait();
                    resetGame();
                }
            }

            if (isGameOver()) {
                Alert alert = new Alert(Alert.AlertType.INFORMATION);
                alert.setTitle("Game Over");
                alert.setHeaderText(null);
                alert.setContentText("Game Over!");
                alert.showAndWait();
                resetGame();
            }

        }
    }

    private boolean isMoveAvailable(int row, int col) {
        // Check if the cell is empty
        if (board[row][col] != 0) {
            return false;
        }

        // Check if the cell is stacked directly on the left or right wall
        if (col == 0 || col == 7) {
            return true;
        }

        if(row == 0 && col == 0) {
            return true;
        }

        if(row == 7 && col == 7) {
            return true;
        }

        if(row == 7 && col == 0) {
            return true;
        }

        if(row == 0 && col == 7) {
            return true;
        }

        // Check if the cell is stacked to the left or right of another brick
        if (col > 0 && board[row][col - 1] != 0) {
            return true;
        }
        if (col < 7 && board[row][col + 1] != 0) {
            return true;
        }

        return false;
    }

    private GridPane createGridPane() {
        GridPane gridPane = new GridPane();
        autoBoard = new Label[8][8];
        boolean isWhiteSquare = true;
        for (int row = 0; row < 8; row++) {
            isWhiteSquare = !isWhiteSquare;
            for (int col = 0; col < 8; col++) {
                Label label = new Label();
                label.setPrefSize(50, 50);
                label.setStyle("-fx-border-color: black;");
                label.setStyle(isWhiteSquare ? "-fx-background-color: white;" : "-fx-background-color: gray;");
                label.setAlignment(Pos.CENTER);
                label.setOnMouseClicked(this::handleLabelClick);
                gridPane.add(label, col, row);
                autoBoard[row][col] = label;
                isWhiteSquare = !isWhiteSquare;
            }
        }


        return gridPane;
    }

    private void placeSign (int row, int col, int player) {
        if (isMoveAvailable(row, col)) {
            if(player == 1) {
                board[row][col] = 1;
                clickCounter++;
                autoBoard[row][col].setText("■");
            }
            if(player == 2) {
                board[row][col] = 2;
                autoBoard[row][col].setText("□");
                clickCounter++;
            }
        } else {
            System.out.println("Invalid move. Please try again.");
        }
    }

    private void handleLabelClick(MouseEvent event) {
        Label clickedLabel = (Label) event.getSource();
        int row = GridPane.getRowIndex(clickedLabel);
        int col = GridPane.getColumnIndex(clickedLabel);
        boolean ck2 = checkWinner(board, 2);
        if(ck2) {
            board = new int[8][8];
            Alert alert = new Alert(Alert.AlertType.INFORMATION);
            alert.setTitle("Player 2 wins!");
            alert.setHeaderText(null);
            alert.setContentText("Player 2 wins!");
            alert.showAndWait();
            resetGame();
        }
        boolean ck3 = checkWinner(board, 2);
        if(ck3) {
            board = new int[8][8];
            Alert alert = new Alert(Alert.AlertType.INFORMATION);
            alert.setTitle("Player 2 wins!");
            alert.setHeaderText(null);
            alert.setContentText("Player 2 wins!");
            alert.showAndWait();
            resetGame();
        }
        if(flag1 == 10) {
            if(clickCounter %2 == 0) {
                placeSign(row, col, 1);
            }
            boolean ck = checkWinner(board, 1);
            if(ck) {
                board = new int[8][8];
                Alert alert = new Alert(Alert.AlertType.INFORMATION);
                alert.setTitle("Player 1 wins!");
                alert.setHeaderText(null);
                alert.setContentText("Player 1 wins!");
                alert.showAndWait();
                resetGame();
            }
           PauseTransition pause = new PauseTransition(Duration.seconds(1));
            pause.setOnFinished(e -> {
                // Call the method to determine the next move after the delay
                Move bestMove = Heuristic(2,1);
                if(clickCounter % 2 != 0) {
                    placeSign(bestMove.getRow(), bestMove.getCol(), 2);
                }

            });
            pause.play();
           
           
        }
        if(flag2 == 10) {

            if(clickCounter %2 != 0) {
                placeSign(row, col, 2);
            }
            boolean ck = checkWinner(board, 2);
            if(ck) {
                board = new int[8][8];
                Alert alert = new Alert(Alert.AlertType.INFORMATION);
                alert.setTitle("Player 2 wins!");
                alert.setHeaderText(null);
                alert.setContentText("Player 2 wins!");
                alert.showAndWait();
                resetGame();
            }
            else {
            	PauseTransition pause = new PauseTransition(Duration.seconds(1));
                pause.setOnFinished(e -> {
                    // Call the method to determine the next move after the delay
                    Move bestMove = Heuristic(1,2);
                    
                    placeSign(bestMove.getRow(), bestMove.getCol(), 1);

                });
                pause.play();


            }
            boolean ck4 = checkWinner(board, 1);
            if(ck4) {
                board = new int[8][8];
                Alert alert = new Alert(Alert.AlertType.INFORMATION);
                alert.setTitle("Player 1 wins!");
                alert.setHeaderText(null);
                alert.setContentText("Player 1 wins!");
                alert.showAndWait();
                resetGame();
            }
            PauseTransition pause = new PauseTransition(Duration.seconds(3));
            pause.setOnFinished(e -> {

            });
            pause.play();

        }

    }

    private boolean isGameOver() {
        return clickCounter == 64;
    }

    private void resetGame() {
        clickCounter = 0;
        board = new int[8][8];
    }

    private Button createLabel(String text) {
        Button label = new Button(text);
        label.setStyle("-fx-font-size: 18px;");
        label.setDisable(true);
        return label;
    }

    public boolean checkWinner(int arr[][], int detBit) {
        // check for rows
        for (int i = 0; i < 8; i++) {
            int counter = 0;
            for (int j = 0; j < 8; j++) {
                if (arr[i][j] == detBit) {
                    counter++;
                    if (counter == 5) {
                        return true;
                    }
                } else {
                    counter = 0;
                }

            }
        }

        // check for columns
        for (int i = 0; i < 8; i++) {
            int counter = 0;
            for (int j = 0; j < 8; j++) {
                if (arr[j][i] == detBit) {
                    counter++;
                    if (counter == 5) {
                        return true;
                    }
                } else {
                    counter = 0;
                }
            }
        }

        // check for diagonal
        for (int row = 0; row < 8; row++) {
            for (int col = 0; col < 8; col++) {
                if (col + 4 < 8 && row + 4 < 8) {
                    boolean hasWon = true;
                    for (int i = 0; i < 5; i++) {
                        if (board[row + i][col + i] != detBit) {
                            hasWon = false;
                            break;
                        }
                    }
                    if (hasWon) {
                        return true;
                    }
                }
                if (col - 4 >= 0 && row + 4 < 8) {
                    boolean hasWon = true;
                    for (int i = 0; i < 5; i++) {
                        if (board[row + i][col - i] != detBit) {
                            hasWon = false;
                            break;
                        }
                    }
                    if (hasWon) {
                        return true;
                    }
                }
            }
        }
        return false;
    }
    
    

    public Move Heuristic(int aiPlayer, int player) {
    	int bestScore = Integer.MIN_VALUE;
        Move bestMove = null;
        ArrayList<Move> availableMoves = new ArrayList<>();

        // Iterate through the board
        for (int row = 0; row < 8; row++) {
            for (int col = 0; col < 8; col++) {
                if (isMoveAvailable(row, col)) {
                	Move newMove = new Move(row, col);
                    availableMoves.add(newMove);
                }
            }
        }
        
        for(int i=0; i<availableMoves.size(); i++) {
        	int row = availableMoves.get(i).getRow();
        	int col = availableMoves.get(i).getCol();
        	board [row][col] = 2;
        	int score = minimax(player, 3, Integer.MIN_VALUE, Integer.MAX_VALUE);
        	board [row][col] = 0;
        	if (score > bestScore) {
                bestScore = score;
                bestMove = availableMoves.get(i);
            }
        }
        
     // check for rows
     		for (int i = 0; i < 8; i++) {
     			int counter = 0;
     			for (int j = 0; j < 8; j++) {
     				if (board[i][j] == 1) {
     					counter++;
     					if (counter == 4) {
     						if(j+1 < 8 && isMoveAvailable(i, j+1)) {
     							bestMove.setCol(j+1);
     							bestMove.setRow(i);
     						}
     						if(j-1 > 0 && isMoveAvailable(i, j-1)) {
     							bestMove.setCol(j-1);
     							bestMove.setRow(i);
     						}
     					}
     					if(counter == 3) {
     						if(j+1 < 8 && isMoveAvailable(i, j+1)) {
     							bestMove.setCol(j+1);
     							bestMove.setRow(i);
     						}
     						if(j-1 > 0 && isMoveAvailable(i, j-1)) {
     							bestMove.setCol(j-1);
     							bestMove.setRow(i);
     						}
     					}
     
     				} else {
     					counter = 0;
     				}

     			}
     		}

     		// check for columns
     		for (int i = 0; i < 8; i++) {
     			int counter = 0;
     			for (int j = 0; j < 8; j++) {
     				if (board[j][i] == 1) {
     					counter++;
     					if (counter == 4) {
     		                if (j + 1 < 8 && isMoveAvailable(j + 1, i)) {
     		                    bestMove.setRow(j + 1);
     		                    bestMove.setCol(i);
     		                    return bestMove;
     		                }
     		                if (j + 1 >= 8 && isMoveAvailable(3, i)) {
     		                    bestMove.setRow(3);
     		                    bestMove.setCol(i);
     		                    return bestMove;
     		                }
     		                if (j - 1 >= 0 && isMoveAvailable(j - 1, i)) {
     		                    bestMove.setRow(j - 1);
     		                    bestMove.setCol(i);
     		                    return bestMove;
     		                }
     		                if (j - 1 < 0 && isMoveAvailable(4, i)) {
     		                    bestMove.setRow(4);
     		                    bestMove.setCol(i);
     		                    return bestMove;
     		                }
     		            }
     					if (counter == 3) {
     						if (j + 1 < 8 && isMoveAvailable(j + 1, i)) {
     		                    bestMove.setRow(j + 1);
     		                    bestMove.setCol(i);
     		                    return bestMove;
     		                }
     		                if (j + 1 >= 8 && isMoveAvailable(4, i)) {
     		                    bestMove.setRow(4);
     		                    bestMove.setCol(i);
     		                    return bestMove;
     		                }
     		                if (j - 1 >= 0 && isMoveAvailable(j - 1, i)) {
     		                    bestMove.setRow(j - 1);
     		                    bestMove.setCol(i);
     		                    return bestMove;
     		                }
     		                if (j - 1 < 0 && isMoveAvailable(3, i)) {
     		                    bestMove.setRow(3);
     		                    bestMove.setCol(i);
     		                    return bestMove;
     		                }
     					    
     					}
     					if(j-2 >= 0 && board[j-1][i] == 1 && board[j-2][i] == 1 && j+1<8 && board[j+1][i] == 1 && j+2<8 &&board[j+2][i] == 1 && isMoveAvailable(j-2, i)) {
     						bestMove.setRow(j);
     						bestMove.setCol(i);
     						return bestMove;
     					}
     					
     					
     				} else {
     					counter = 0;
     				}
     			}
     		}
     		
     	// Check for diagonals (left-top to right-bottom)
     		for (int i = 0; i < 5; i++) {
     		    for (int j = 0; j < 5; j++) {
     		        int counter = 0;
     		        for (int k = 0; k < 5; k++) {
     		        	if(i+k < 8 && j+k < 8) {
     		        		if (board[i + k][j + k] == 1 ) {
         		                counter++;
         		                if (counter == 4) {
         		                    if (i + k + 1 < 8 && j + k + 1 < 8 && isMoveAvailable(i + k + 1, j + k + 1) && board[i + k + 1][j + k + 1] != 2) {
         		                        bestMove.setRow(i + k + 1);
         		                        bestMove.setCol(j + k + 1);
         		                        return bestMove;
         		                    }
         		                    if (i + k - 4 >= 0 && j + k - 4 >= 0 && isMoveAvailable(i + k - 4, j + k - 4) && board[i + k - 4][j + k - 4] != 2) {
         		                        bestMove.setRow(i + k - 4);
         		                        return bestMove;
         		                    }
         		                }
         		            } else {
         		                counter = 0;
         		            }
     		        	}
     		            
     		        }
     		    }
     		}

     		// Check for diagonals (right-top to left-bottom)
     		for (int i = 0; i < 5; i++) {
     		    for (int j = 7; j > 2; j--) {
     		        int counter = 0;
     		        for (int k = 0; k < 5; k++) {
     		        	if(i+k < 8 && j - k > 0) {
     		        		if (board[i + k][j - k] == 1) {
         		                counter++;
         		                if (counter == 4) {
         		                    if (i + k + 1 < 8 && j - k - 1 >= 0 && isMoveAvailable(i + k + 1, j - k - 1) && board[i + k + 1][j - k - 1] != 2) {
         		                        bestMove.setRow(i + k + 1);
         		                        bestMove.setCol(j - k - 1);
         		                        return bestMove;
         		                    }
         		                    if (i + k - 4 >= 0 && j - k + 4 < 8 && isMoveAvailable(i + k - 4, j - k + 4) && board[i + k - 4][j - k + 4] != 2) {
         		                        bestMove.setRow(i + k - 4);
         		                        bestMove.setCol(j - k + 4);
         		                        return bestMove;
         		                    }
         		                }
         		            } else {
         		                counter = 0;
         		            }
     		        	}
     		            
     		        }
     		    }
     		}
     	// Check for diagonal wins
     	    for (int i = 0; i < availableMoves.size(); i++) {
     	        int row = availableMoves.get(i).getRow();
     	        int col = availableMoves.get(i).getCol();
     	        board[row][col] = 2;
     	        int score = minimax(player, 4, Integer.MIN_VALUE, Integer.MAX_VALUE);
     	        board[row][col] = 0;
     	        if (score > bestScore) {
     	            bestScore = score;
     	            bestMove = availableMoves.get(i);
     	        }
     	    }

     	    // Check for column wins
     	    for (int col = 0; col < 8; col++) {
     	        for (int row = 0; row < 5; row++) {
     	            if (board[row][col] == 0 && board[row + 1][col] == 2 && board[row + 2][col] == 2 && board[row + 3][col] == 2 && board[row + 4][col] == 0) {
     	                bestMove.setRow(row);
     	                bestMove.setCol(col);
     	                return bestMove;
     	            }
     	        }
     	    }
        return bestMove;
    }
    
    
////////////////////////////////////////////////////////////////////////////

    /**
     * Performs the minimax algorithm with alpha-beta pruning to determine the best score for the current player.
     *
     * @param board                The current state of the game board.
     * @param depth                The current depth of the search tree.
     * @param alpha                The best score that the maximizing player can guarantee.
     * @param beta                 The best score that the minimizing player can guarantee.
     * @param isMaximizingPlayer   A boolean flag indicating if the current player is the maximizing player.
     * @return                     The best score for the current player at the given state.
     */
    public int minimax(int player, int depth, int alpha, int beta) {
        if (depth == 0 || isGameOver()) {
            return evaluate(player);
        }

        if (player == 1) {
            int bestScore = Integer.MIN_VALUE;
            for (int row = 0; row < 8; row++) {
                for (int col = 0; col < 8; col++) {
                    if (isMoveAvailable(row, col) && board[row][col] == 0) {
                        board[row][col] = player;
                        int score = checkWinner(board, player) ? 1 : minimax(player, depth - 1, alpha, beta);
                        board[row][col] = 0;
                        bestScore = Math.max(score, bestScore);
                        alpha = Math.max(alpha, bestScore);
                        if (alpha >= beta) {
                            break;
                        }
                    }
                }
                if (alpha >= beta) {
                    break;
                }
            }
            return bestScore;
        } else { // player == BRICK2
            int bestScore = Integer.MAX_VALUE;
            for (int row = 0; row < 8; row++) {
                for (int col = 0; col < 8; col++) {
                    if (isMoveAvailable(row, col) && board[row][col] == 0) {
                        board[row][col] = player;
                        int score = checkWinner(board, player) ? -1 : minimax(player, depth - 1, alpha, beta);
                        board[row][col] = 0;
                        bestScore = Math.min(score, bestScore);
                        beta = Math.min(beta, bestScore);
                        if (alpha >= beta) {
                            break;
                        }
                    }
                }
                if (alpha >= beta) {
                    break;
                }
            }
            return bestScore;
        }
    }
    
    private int evaluate(int player) {
        int score = 0;

        // Check for row wins
        for (int i = 0; i < 8; i++) {
            int counter = 0;
            for (int j = 0; j < 8; j++) {
                if (board[i][j] == player) {
                    counter++;
                    if (counter >= 3) {
                        score += counter * counter;
                    }
                } else {
                    counter = 0;
                }
            }
        }

        // Check for column wins
        for (int i = 0; i < 8; i++) {
            int counter = 0;
            for (int j = 0; j < 8; j++) {
                if (board[j][i] == player) {
                    counter++;
                    if (counter >= 3) {
                        score += counter * counter;
                    }
                } else {
                    counter = 0;
                }
            }
        }

        // Check for diagonal wins (left-top to right-bottom)
        for (int i = 0; i < 5; i++) {
            for (int j = 0; j < 5; j++) {
                int counter = 0;
                for (int k = 0; k < 5; k++) {
                    if (i + k < 8 && j + k < 8) {
                        if (board[i + k][j + k] == player) {
                            counter++;
                            if (counter >= 3) {
                                score += counter * counter;
                            }
                        } else {
                            counter = 0;
                        }
                    }
                }
            }
        }

        // Check for diagonal wins (right-top to left-bottom)
        for (int i = 0; i < 5; i++) {
            for (int j = 7; j > 2; j--) {
                int counter = 0;
                for (int k = 0; k < 5; k++) {
                    if (i + k < 8 && j - k > 0) {
                        if (board[i + k][j - k] == player) {
                            counter++;
                            if (counter >= 3) {
                                score += counter * counter;
                            }
                        } else {
                            counter = 0;
                        }
                    }
                }
            }
        }

        return score;
    }

///////////////////////////////////////////////////////////////////////////////////////////


    private int evaluateBoard(int [][] board) {
        int player1Count = 0;
        int player2Count = 0;

        // Count the number of bricks for each player
        for (int row = 0; row < 8; row++) {
            for (int col = 0; col < 8; col++) {
                if (board[row][col] == 1) {
                    player1Count++;
                } else if (board[row][col] == 2) {
                    player2Count++;
                }
            }
        }

        // Calculate the score based on the brick counts
        int score = player1Count - player2Count;

        return score;
    }


    public class Move {
        private int row;
        private int col;

        public Move(int row, int col) {
            this.row = row;
            this.col = col;
        }

        public int getRow() {
            return row;
        }

        public int getCol() {
            return col;
        }

        public void setRow(int row) {
            this.row = row;
        }

        public void setCol(int col) {
            this.col = col;
        }
    }
    
 public class HuristicResult {
    private boolean detbit;
    private  int row_blankLocation;
     private  int column_blankLocation;

     public HuristicResult() {

     }

        public HuristicResult(boolean detbit) {
            this.detbit = detbit;
        }

        public HuristicResult(boolean detbit, int row_blankLocation, int column_blankLocation) {
         this.detbit = detbit;
         this.row_blankLocation = row_blankLocation;
         this.column_blankLocation=column_blankLocation;
     }

     public boolean isDetbit() {
         return detbit;
     }

     public int getRow_blankLocation() {
         return row_blankLocation;
     }

     public int getColumn_blankLocation() {
         return column_blankLocation;
     }


     public boolean  det() {
         return detbit;
     }

     public void setDetbit(boolean detbit) {
         this.detbit = detbit;
     }

     public void setRow_blankLocation(int row_blankLocation) {
         this.row_blankLocation = row_blankLocation;
     }

     public void setColumn_blankLocation(int column_blankLocation) {
         this.column_blankLocation = column_blankLocation;
     }
 }
    /////////////////////////////////////////////////////////////////////////
    public static void main(String[] args) {
        launch(args);
    }
}
