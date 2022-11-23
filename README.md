# Plusklass

_Introduction_

Backtracking is an algorithmic technique used for finding solutions to a problem recursively by incrementally building the solution by considering all potential combinations and abandoning the ones that do not satisfy the problem constraints.

Backtracking algorithms are based on the DFS (Depth First Search) approach. 

Backtracking is generally used for three types of problems:

    Decision Problem: Searching for a feasible solution.
    Optimization Problem: Searching for the best solution.
    Enumeration Problem: Searching for all feasible solutions.

Problem Discussion :

    You are given a 2d array of dimensions n*m.

    This array is filled with either 0 or 1. You can only move to cells which have 0 value in them. You can't move out of the boundaries or in the cells which have value 1 in them (1 means obstacle)

    Only four moves are allowed: 't' (1-step up), 'l' (1-step left), 'd' (1-step down) 'r' (1-step right) in the given order only.

    You are required to print all paths that can be used to move from top-left to bottom-right.

    We have to print all the paths from top left to bottom left cell such that no path passes from the same cell more than once.
    
    Input:-   3 3
              0 0 0
              1 0 1
              0 0 0
    Output:-  rddr

FIRST TRY




Approach :

We are going to first simply write all the recursive functions for reaching the top, left, bottom and right cells.
This is a brute approach.
Then we also write the base cases to catch all the unwanted situations.

      import java.io.*;
      import java.util.*;
      public class Main {
        public static void main(String[] args) throws Exception {                    Scanner scn = new Scanner(System.in);
          int n = scn.nextInt();
          int m = scn.nextInt();
          int[][] arr = new int[n][m];
          for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
              arr[i][j] = scn.nextInt();
            }
          }
          floodfill(arr, 0, 0, "");
        }                                           // asf -> answer so far               
        public static void floodfill(int[][] maze, int sr, int sc, String asf) {               
            if(sr<0 ||sc<0||sr==maze.length|| sc==maze[0].length|| maze[sr][sc]==1){                    
                return;         //1                
             }                
             if(sr==maze.length-1 && sc==maze[0].length-1){   //2                  
                 System.out.println(asf);                   
                 return;               
             }                             
             floodfill(maze,sr-1,sc,asf+"t");  //3               
             floodfill(maze,sr,sc-1,asf+"l");  //4                
             floodfill(maze,sr+1,sc,asf+"d");  //5                
             floodfill(maze,sr,sc+1,asf+"r");  //6 
        }
        
 Let's analyze the working of this code, only then we will be able to catch any mistakes in it .

We pass the following arguments as the parameters in the function "floodfill".


BASE CASE : It suggests if the row and column ever goes out of the grid i.e. if they are less than 0 or greater than the row or column of the given array then the function will be returned .

Here the order of the "Or (||)" statements matter. You can't check the condition of maze[sr][sc]==1 first .This is because even if one of the OR statements is true then true is returned without checking the other conditions. Had maze[sr][sc]==1 been checked before sr<0 when the row actually were negative then an Exception would have been thrown that maze[sr][sc] can't be checked because a negative row doesn't exist in the array.

Another base case is that when row and column become equal to the last indices on the grid then "Answer so far" is printed and the function is returned.

Then using our basic knowledge of recursion we call the function "floodfill" recursively for top, left, down and right steps.

However, if we run this code, we get a RUNTIME ERROR "STACK OVERFLOW".

Where did this come from? Didn't we do everything right?

No. We didn't. Let's see WHY by dry running this code.

We can see in figure 4, when we are initially at maze[0][0], //1 and //2 are checked and we are allowed to go further. Then, the recursive function for 't' is run which fails the base case as sr<0 and we move to the 'l' call. Again the base case fails. Now we move to the 'd' statement. As it runs we move a step down and reach maze[1][0].

maze[1][0] passes the test cases and we move a step up. We again reach the cell we were at previously.

Again for maze[0][0] we run the code and like earlier reach maze[1][0] again. Hence we keep moving up and down infinitely.This can also be seen from the stack .

We need to break this infinite recursion. Let's see how.

We need to do something so that if we have visited a cell already we don't have to visit it again. Look at the code given below and notice the changes.


SECOND TRY

Approach :


Here, we pass an empty boolean 2d array in the function as a parameter called "visited[][]" which has the dimensions same as are our input array.

Before visiting a cell in the maze array , we mark the corresponding cell in the visited array " true" so that in the duration of the same path we don't visit the already visited cell again.

        import java.io.*;
        import java.util.*;
        public class Main {
          public static void main(String[] args) throws Exception {                      Scanner scn = new Scanner(System.in);
            int n = scn.nextInt();
            int m = scn.nextInt();
            int[][] arr = new int[n][m];
            for (int i = 0; i < n; i++) {
              for (int j = 0; j < m; j++) {
                arr[i][j] = scn.nextInt();
              }
            }
            boolean[][] visited = new boolean[n][m];
            floodfill(arr, 0, 0, "", visited);
          }                                 public static void floodfill(int[][] maze, int sr, int sc, String asf, boolean[][] visited) {
            if (sr < 0 || sc < 0 || sr == maze.length || sc == maze[0].length || maze[sr][sc] == 1 ||                  visited[sr][sc] == true) {
              return;
            }
            if (sr == maze.length - 1 && sc == maze[0].length - 1) {
              System.out.println(asf);
              return;
            }
            visited[sr][sc] = true;
            floodfill(maze, sr - 1, sc, asf + "t", visited);
            floodfill(maze, sr, sc - 1, asf + "l", visited);
            floodfill(maze, sr + 1, sc, asf + "d", visited);
            floodfill(maze, sr, sc + 1, asf + "r", visited);
          }

CODE DISCUSSION

For this we write a condition in Base Case of the problem which checks whether we have already visited that cell of the array or not.

To resolve the previous mistake we took the help of the concept of Dynamic programming by introducing a visited array. You will learn more about it later.

Let's try to dry run the code till now and check if it is the final code or not.
 Then we make recursive calls for 't', 'l' ,'d' and 'r' . You can see the path that running the code makes.

We reach the end of this path without reaching the destination because either the cells around that cell have already been visited before or they are obstacles. Hence we have no option to move forward and therefore we retreat.

For retreating, a level of stack keeps popping as we return to the previous level in the stack because the test case failed.

As we keep on doing it we reach the position when the situation looks like this:


Now, we reach the cell marked with "tl". Then we try to move down but can't because the cell is visited .

Then we try to move right however we are not able to because the cell is marked visited even though no path goes through it !

So do you realize our mistake in the previous code? We retreated from the path without unmarking the cells along that path.

Hence we were not able to continue by going on the previous path even after that previous path was wiped out earlier.

To correct our mistake we make a small modification in our code as you can see below:


THIRD TRY

Approach :


Reader, after all the recursion calls have run completely and returned, we mark that cell as false again as seen in the code given below.


        import java.io.*;
        import java.util.*;
        public class Main {
          public static void main(String[] args) throws Exception {                    Scanner scn = new Scanner(System.in);
            int n = scn.nextInt();
            int m = scn.nextInt();
            int[][] arr = new int[n][m];
            for (int i = 0; i < n; i++) {
              for (int j = 0; j < m; j++) {
                arr[i][j] = scn.nextInt();
              }
            }
            boolean[][] visited = new boolean[n][m];
            floodfill(arr, 0, 0, "", visited);
          }                         public static void floodfill(int[][] maze, int sr, int sc, String asf, boolean[][] visited) {
            if (sr < 0 || sc < 0 || sr == maze.length || sc == maze[0].length || maze[sr][sc] == 1 || visited[sr][sc] == true) {
              return;
            }
            if (sr == maze.length - 1 && sc == maze[0].length - 1) {
              System.out.println(asf);
              return;
            }
            visited[sr][sc] = true;
            floodfill(maze, sr - 1, sc, asf + "t", visited);
            floodfill(maze, sr, sc - 1, asf + "l", visited);
            floodfill(maze, sr + 1, sc, asf + "d", visited);
            floodfill(maze, sr, sc + 1, asf + "r", visited);
            visited[sr][sc] = false;
          }
          
   CODE DISCUSSION

Since after all the recursion calls have run completely and returned, we mark that cell as false again, therefore we are free to move on that cell if we want to.

Now when we retreat , the ticks are also wiped out as can been seen below:


When this is the case, we again try to look for a new path which is illustrated by Figure 8.2.


In the previous traversal we tried to go on the path 8.1 and after the path was retreated and we once again marked the visited cells as unvisited, we went looking for a new path.

However both of these paths are undesirable because they meet obstacles.

Hence we retreat again 


Now we start finding a new path which ultimately leads us to our destination.


Hence we have found our first path "ddrdddrrrrr".

This is only the first path and when we return from the previous recursive processes we start retreating again until we reach another stop from which a new path can be continued.


We started wiping out the cells until we reached the position as shown in figure 11.

Again the cells are visited and unvisited multiple times until we find a new path.


Hence, the code in the "Third Try" gives us the desirable outputs so it is our final code.



Analysis

Time Complexity :


O(4*n2) which can simply be written as O(n2).

This is because each cell of the matrix is processed at most 4 times. For Example, a particular cell can be called by its top, down, left or right cell.



SPACE COMPLEXITY :


O(n2)

Since an extra 2D array is used for storing "visited" cells therefore the space complexity is quadratic.


With this we conclude our article.
