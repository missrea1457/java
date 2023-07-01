# x_o_game
#java
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

class Node {
	List<Node> children = new ArrayList<Node>();
	int[][] value;
	Node parents = null;
	int score = 0;
	boolean Max;
	int alpha = -2;
	int beta = 2;

	public Node(int[][] value, boolean max, int score) {
		this.value = value;
		this.Max = max;
		this.score = score;
	}

	public static Node insert(Node parent, Node child) {
		parent.children.add(child);
		child.parents = parent;
		return child;
	}
}

public class X_O_game {
	static boolean checking_end(int[][] X_O) {
		for (int i = 0; i < 3; i++) {
			for (int j = 0; j < 3; j++) {
				if (X_O[i][j] == 0) {
					return false;
				}
			}
		}
		return true;

	}

	public static int checking_win(int[][] X_O) {
		/////////
		int sum = 0;
		for (int i = 0; i < 3; i++) {
			sum += X_O[i][i];
		}
		if (sum == -3 || sum == 3)
			return sum / 3;
		/////

		for (int i = 0; i < 3; i++) {
			int sum1 = 0, sum2 = 0;
			for (int j = 0; j < 3; j++) {
				sum1 += X_O[i][j];
				sum2 += X_O[j][i];
			}

			if (sum1 == -3 || sum1 == 3)
				return sum1 / 3;
			if (sum2 == -3 || sum2 == 3)
				return sum2 / 3;

		}
/////////////
		int sum3 = 0;
		for (int i = 0; i < 3; i++) {
			sum3 += X_O[i][2 - i];
		}
		if (sum3 == -3 || sum3 == 3)
			return sum3 / 3;

		return 0;
	}

	static void print(int X_O[][]) {
		for (int i = 0; i < 3; i++) {
			for (int j = 0; j < 3; j++) {
				if (j < 2) {
					if (X_O[i][j] == -1)
						System.out.print("X | ");
					else if (X_O[i][j] == 1) {
						System.out.print("O | ");
					} else
						System.out.print("  | ");
				} else {
					if (X_O[i][j] == -1)
						System.out.print("X ");
					else if (X_O[i][j] == 1) {
						System.out.print("O");
					}

				}
			}
			System.out.println("");
		}
		System.out.println("---------");
	}

	static boolean place(int[][] X_O, int user, int w) {
		int col = user % 3;
		int row = user / 3;
		if (X_O[row][col] != 0) {
			return false;
		} else {
			X_O[row][col] = w;
			return true;
		}

	}

	static int[][] copy(int[][] Array) {
		int Array1[][] = new int[3][3];
		for (int i = 0; i < 3; i++) {
			for (int j = 0; j < 3; j++) {
				Array1[i][j] = Array[i][j];
			}
		}
		return Array1;
	}

	static boolean out_of_range(Node head, Node prev) {
		if (head.Max) {
			if (prev.score > head.beta)
				return true;
		} else {
			if (prev.score < head.alpha) {
				return true;
			}

		}
		return false;
	}

	static Node minmax(Node head) {

		int Array[][] = head.value;
		if (!checking_end(Array) && checking_win(Array) == 0) {
			for (int i = 0; i < 3; i++) {
				for (int j = 0; j < 3; j++) {
					if (Array[i][j] == 0) {
						int val[][] = copy(Array);
						if (head.Max) {
							val[i][j] = 1;
						} else {
							val[i][j] = -1;
						}
						Node.insert(head, new Node(val, !head.Max, checking_win(val)));
					}
				}
			}
		}

		if (head.parents != null) {
			head.alpha = head.parents.alpha;
			head.beta = head.parents.beta;
		}
		int select = 0;
		if (head.children.size() == 0) {

			return head;
		}
		for (int i = 0; i < head.children.size(); i++) {
			Node prev = minmax(head.children.get(i));

			if (head.Max) {
				if (head.alpha < prev.score) {
					head.alpha = prev.score;
					select = i;
				}
			} else {
				if (head.beta > prev.score) {
					head.beta = prev.score;
					select = i;

				}
			}
			if (out_of_range(head, prev)) {
				break;
			}
		}

		head.score = head.children.get(select).score;
		return head.children.get(select);
	}

	public static void main(String[] args) {
		Scanner scn = new Scanner(System.in);
		int X_O[][] = new int[3][3];
		for (int i = 0; i < 3; i++) {
			for (int j = 0; j < 3; j++) {
				X_O[i][j] = 0;
			}
		} //
		String x = "1 | 2 | 3\n4 | 5 | 6\n7 | 8 | 9\n---------\nEnter a number between 1 and 9\n";
		System.out.print(x);
		while (true) {
			int user = scn.nextInt();
			user--;
			if (!place(X_O, user, -1)) {
				System.out.print("not empty");
				continue;
			} // if
			print(X_O);
			if (checking_win(X_O) == -1) {
				System.out.print("user win");
				break;
			}
			Node head = new Node(X_O, true, 0);
			Node system = minmax(head);
			X_O = system.value;
			print(system.value);
			if (checking_win(X_O) == 1) {
				System.out.print("user lose");
				break;
			}
			if (checking_end(X_O)) {
				System.out.print("ended");
				break;

			}
		} // while
	}// main

}// class
