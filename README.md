import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
import java.util.Scanner;

public class StudentManagement {

    public static void main(String[] args) {

        // 1. Create a list with 10 students
        List<Student> studentList = new ArrayList<>();
        studentList.add(new Student(1, "Alice", "Smith", 20, 4.5));
        studentList.add(new Student(2, "Bob", "Johnson", 22, 3.8));
        studentList.add(new Student(3, "Charlie", "Brown", 21, 4.0));
        studentList.add(new Student(4, "David", "Lee", 19, 4.8));
        studentList.add(new Student(5, "Eve", "Wilson", 23, 3.5));
        studentList.add(new Student(6, "Fiona", "Garcia", 20, 4.2));
        studentList.add(new Student(7, "George", "Rodriguez", 22, 3.9));
        studentList.add(new Student(8, "Hannah", "Martinez", 21, 4.6));
        studentList.add(new Student(9, "Ivan", "Anderson", 19, 4.9));
        studentList.add(new Student(10, "Jack", "Thomas", 23, 3.7));

        // Print the original list
        System.out.println("Original Student List:");
        printStudentList(studentList);

        // 2. Sort the list by average grade
        Collections.sort(studentList, Comparator.comparingDouble(Student::getAverageGrade));
        System.out.println("\nSorted Student List by Average Grade:");
        printStudentList(studentList);


        // 3. Remove students with average grade less than a given value
        Scanner scanner = new Scanner(System.in);
        System.out.print("\nEnter the minimum average grade to keep: ");
        double minAverageGrade = scanner.nextDouble();

        studentList.removeIf(student -> student.getAverageGrade() < minAverageGrade);

        System.out.println("\nStudent List after removing students with average grade less than " + minAverageGrade + ":");
        printStudentList(studentList);
        scanner.close();
    }

    private static void printStudentList(List<Student> studentList) {
        for (Student student : studentList) {
            System.out.println(student); // Assuming Student class has a proper toString() method
        }
    }
}

class Student {
    private int id;
    private String name;
    private String lastName;
    private int age;
    private double averageGrade;

    public Student(int id, String name, String lastName, int age, double averageGrade) {
        this.id = id;
        this.name = name;
        this.lastName = lastName;
        this.age = age;
        this.averageGrade = averageGrade;
    }

    // Getters and setters
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public double getAverageGrade() {
        return averageGrade;
    }

    public void setAverageGrade(double averageGrade) {
        this.averageGrade = averageGrade;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", lastName='" + lastName + '\'' +
                ", age=" + age +
                ", averageGrade=" + averageGrade +
                '}';
    }
}










import java.io.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class CheckersGame {

    // Constants
    private static final int BOARD_SIZE = 8;
    private static final String FILE_NAME = "checkers_game.txt";

    // Game state
    private char[][] board;
    private List<String> moves;
    private int currentPlayer; // 1 - red, 2 - black
    private boolean gameInProgress;

    public CheckersGame() {
        board = new char[BOARD_SIZE][BOARD_SIZE];
        moves = new ArrayList<>();
        currentPlayer = 1; // Red starts
        gameInProgress = false;
        loadGame(); // Load the game state from file
    }

    // Initialization & Loading

    private void initializeBoard() {
        for (int row = 0; row < BOARD_SIZE; row++) {
            for (int col = 0; col < BOARD_SIZE; col++) {
                if ((row + col) % 2 != 0) { // Only set pieces on dark squares
                    if (row < 3) {
                        board[row][col] = 'r'; // Red
                    } else if (row > 4) {
                        board[row][col] = 'b'; // Black
                    } else {
                        board[row][col] = ' '; // Empty
                    }
                } else {
                    board[row][col] = ' '; // Empty
                }
            }
        }
    }

    private void loadGame() {
        try (BufferedReader reader = new BufferedReader(new FileReader(FILE_NAME))) {
            String line;
            List<String> lines = new ArrayList<>();
            while ((line = reader.readLine()) != null) {
                lines.add(line);
            }

            if (lines.isEmpty()) {
                initializeBoard();
                return; // Start a new game
            }

            // Load board state (assuming it's the first part of the file)
            if (lines.size() >= BOARD_SIZE) {
                for (int row = 0; row < BOARD_SIZE; row++) {
                    if(lines.get(row).length() != BOARD_SIZE) {
                        System.out.println("Warning: corrupted board state in file. Starting new game.");
                        initializeBoard();
                        return;
                    }
                    for (int col = 0; col < BOARD_SIZE; col++) {
                        board[row][col] = lines.get(row).charAt(col);
                    }
                }
            } else {
                 System.out.println("Warning: corrupted board state in file. Starting new game.");
                 initializeBoard();
                 return;
            }


            // Load moves
            for (int i = BOARD_SIZE; i < lines.size(); i++) {
                 moves.add(lines.get(i));
            }

            //Determine the current player based on the number of moves.
            currentPlayer = (moves.size() % 2 == 0) ? 1 : 2;
            gameInProgress = true;
            System.out.println("Game loaded from file.");
            printBoard();


        } catch (IOException e) {
            initializeBoard();
            System.out.println("No saved game found. Starting a new game.");
        }
    }

    // Gameplay Methods

    public void startGame() {
        Scanner scanner = new Scanner(System.in);
        if(!gameInProgress){
            initializeBoard();
        }

        while (true) {
            printBoard();
            System.out.println("Player " + currentPlayer + ", enter your move (e.g., a1-b2 or a1xb3 for capture) or 'quit':");
            String move = scanner.nextLine().trim().toLowerCase();

            if ("quit".equals(move)) {
                saveGame();
                System.out.println("Game saved. Exiting.");
                break;
            }

            if (isValidMove(move)) {
                executeMove(move);
                moves.add(move);
                saveGame();
                if (checkWin()) {
                    printBoard();
                    System.out.println("Player " + currentPlayer + " wins!");
                    break;
                }
                currentPlayer = (currentPlayer == 1) ? 2 : 1;
            } else {
                System.out.println("Invalid move. Try again.");
            }
        }
        scanner.close();
    }

    private boolean isValidMove(String move) {
        if (move.length() < 5) {
            return false; // Minimum length is a1-b2 or a1xb3
        }

        // Simple validation for format
        if (!move.matches("^[a-h][1-8][-x][a-h][1-8]$")) {
            return false;
        }
        return true;
    }

    private void executeMove(String move) {
        // Convert move to coordinates
        int startRow = BOARD_SIZE - Character.getNumericValue(move.charAt(1));
        int startCol = move.charAt(0) - 'a';
        int endRow = BOARD_SIZE - Character.getNumericValue(move.charAt(4));
        int endCol = move.charAt(3) - 'a';

        char piece = board[startRow][startCol];
        char targetPiece = board[endRow][endCol];


        if (targetPiece != ' ') {
            System.out.println("Invalid move: Target square is not empty");
            return;
        }

        // Check if the piece belongs to the current player.
        if ((currentPlayer == 1 && Character.toLowerCase(piece) != 'r') ||
            (currentPlayer == 2 && Character.toLowerCase(piece) != 'b')) {
                System.out.println("Invalid move: You are trying to move the opponent's piece.");
                return;
        }

        // Basic move execution (without checking for captures or promotion)
        board[startRow][startCol] = ' ';
        board[endRow][endCol] = piece;

        //Check for captures (simple implementation - need to improve for multiple captures)
        if(move.charAt(2) == 'x'){
            int capturedRow = (startRow + endRow) / 2;
            int capturedCol = (startCol + endCol) / 2;
            board[capturedRow][capturedCol] = ' ';
        }
    }

    private boolean checkWin() {
        boolean redPieces = false;
        boolean blackPieces = false;

        for (int row = 0; row < BOARD_SIZE; row++) {
            for (int col = 0; col < BOARD_SIZE; col++) {
                if (board[row][col] == 'r') {
                    redPieces = true;
                }
                if (board[row][col] == 'b') {
                    blackPieces = true;
                }
            }
        }

        if (!redPieces) return true; // Black wins
        if (!blackPieces) return true; // Red wins
        return false; // No winner yet
    }

    // File Saving

    private void saveGame() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(FILE_NAME))) {
            // Save board state
            for (int row = 0; row < BOARD_SIZE; row++) {
                writer.write(new String(board[row])); // Write each row as a string
                writer.newLine();
            }

            // Save moves
            for (String move : moves) {
                writer.write(move);
                writer.newLine();
            }
            System.out.println("Game saved.");
        } catch (IOException e) {
            System.err.println("Error saving the game: " + e.getMessage());
        }
    }

    // Display

    private void printBoard() {
        System.out.println("  a b c d e f g h");
        for (int row = 0; row < BOARD_SIZE; row++) {
            System.out.print((BOARD_SIZE - row) + " ");
            for (int col = 0; col < BOARD_SIZE; col++) {
                System.out.print(board[row][col] + " ");
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        CheckersGame game = new CheckersGame();
        game.startGame();
    }
}











ккк
import java.util.Scanner;

class Bus {
    private int totalSeats;      // Total number of seats
    private int occupiedSeats;   // Occupied seats
    private int pricePerSeat;    // Price per seat

    // Constructor for the Bus class
    public Bus(int totalSeats, int pricePerSeat) {
        this.totalSeats = totalSeats;
        this.pricePerSeat = pricePerSeat;
        this.occupiedSeats = 0;   // Initially, all seats are available
    }

    // Method to book seats
    public int bookSeats(int numberOfPeople) {
        if (numberOfPeople <= availableSeats()) {
            occupiedSeats += numberOfPeople; // Increment occupied seats
            return priceForGroup(numberOfPeople); // Return total price
        } else {
            return -1; // Booking failed due to insufficient seats
        }
    }

    // Method to get the number of available seats
    public int availableSeats() {
        return totalSeats - occupiedSeats; // Return the number of free seats
    }

    // Method to calculate the price for a group
    public int priceForGroup(int numberOfPeople) {
        return numberOfPeople * pricePerSeat; // Total cost
    }
}

public class TouristBusApp {
    public static void main(String[] args) {
        // Create two TouristBus objects
        Bus bus1 = new Bus(50, 500); // 50 seats, 500 per seat
        Bus bus2 = new Bus(50, 600); // 50 seats, 600 per seat

        Scanner scanner = new Scanner(System.in); // Scanner for input

        // Input the number of people for the first bus
        int peopleBus1 = inputNumber(scanner, "Enter the number of people for the first bus: ");

        // Input the number of people for the second bus
        int peopleBus2 = inputNumber(scanner, "Enter the number of people for the second bus: ");

        // Book seats and get the price
        int price1 = bus1.bookSeats(peopleBus1);
        int price2 = bus2.bookSeats(peopleBus2);

        // Output information about the first bus
        if (price1 != -1) {
            System.out.println("Available seats on the first bus: " + bus1.availableSeats());
            System.out.println("Total cost for the first group: " + price1 + " rubles");
        } else {
            System.out.println("Not enough seats on the first bus.");
        }

        // Output information about the second bus
        if (price2 != -1) {
            System.out.println("Available seats on the second bus: " + bus2.availableSeats());
            System.out.println("Total cost for the second group: " + price2 + " rubles");
        } else {
            System.out.println("Not enough seats on the second bus.");
        }

        scanner.close(); // Close the scanner
    }

    // Method to input a number and handle invalid input
    private static int inputNumber(Scanner scanner, String prompt) {
        int number = 0;
        boolean validInput = false;

        while (!validInput) {
            System.out.print(prompt);
            if (scanner.hasNextInt()) {
                number = scanner.nextInt();
                if (number > 0) {
                    validInput = true; // Valid input received
                } else {
                    System.out.println("Please enter a positive number!");
                }
            } else {
                System.out.println("Введите число!"); // Message for non-numeric input
                scanner.next(); // Clear the invalid input
            }
        }
        return number;
    }
}







import java.util.Scanner;

class Bus {
    private int totalSeats;      // Total number of seats
    private int occupiedSeats;   // Occupied seats
    private int pricePerSeat;    // Price per seat

    // Constructor for the Bus class
    public Bus(int totalSeats, int pricePerSeat) {
        this.totalSeats = totalSeats;
        this.pricePerSeat = pricePerSeat;
        this.occupiedSeats = 0;   // Initially, all seats are available
    }

    // Method to book seats
    public int bookSeats(int numberOfPeople) {
        if (numberOfPeople <= availableSeats()) {
            occupiedSeats += numberOfPeople; // Increment occupied seats
            return priceForGroup(numberOfPeople); // Return total price
        } else {
            return -1; // Booking failed due to insufficient seats
        }
    }

    // Method to get the number of available seats
    public int availableSeats() {
        return totalSeats - occupiedSeats; // Return the number of free seats
    }

    // Method to calculate the price for a group
    public int priceForGroup(int numberOfPeople) {
        return numberOfPeople * pricePerSeat; // Total cost
    }
}

public class TouristBusApp {
    public static void main(String[] args) {
        // Create two TouristBus objects
        Bus bus1 = new Bus(50, 500); // 50 seats, 500 per seat
        Bus bus2 = new Bus(50, 600); // 50 seats, 600 per seat

        Scanner scanner = new Scanner(System.in); // Scanner for input

        // Input the number of people for the first bus
        System.out.print("Enter the number of people for the first bus: ");
        int peopleBus1 = scanner.nextInt();

        // Input the number of people for the second bus
        System.out.print("Enter the number of people for the second bus: ");
        int peopleBus2 = scanner.nextInt();

        // Book seats and get the price
        int price1 = bus1.bookSeats(peopleBus1);
        int price2 = bus2.bookSeats(peopleBus2);

        // Output information about the first bus
        if (price1 != -1) {
            System.out.println("Available seats on the first bus: " + bus1.availableSeats());
            System.out.println("Total cost for the first group: " + price1 + " rubles");
        } else {
            System.out.println("Not enough seats on the first bus.");
        }

        // Output information about the second bus
        if (price2 != -1) {
            System.out.println("Available seats on the second bus: " + bus2.availableSeats());
            System.out.println("Total cost for the second group: " + price2 + " rubles");
        } else {
            System.out.println("Not enough seats on the second bus.");
        }

        scanner.close(); // Close the scanner
    }
}




import java.util.Scanner;

class Bus {
    private int totalSeats;      // Общее количество мест
    private int occupiedSeats;   // Занятые места
    private int pricePerSeat;    // Цена за место

    // Конструктор класса Bus
    public Bus(int totalSeats, int pricePerSeat) {
        this.totalSeats = totalSeats;
        this.pricePerSeat = pricePerSeat;
        this.occupiedSeats = 0;   // Изначально все места свободны
    }

    // Метод для бронирования мест
    public int bookSeats(int numberOfPeople) {
        if (numberOfPeople <= availableSeats()) {
            occupiedSeats += numberOfPeople; // Увеличиваем занятые места
            return priceForGroup(numberOfPeople); // Возвращаем общую цену
        } else {
            return -1; // Не удалось забронировать места
        }
    }

    // Метод для получения доступного количества мест
    public int availableSeats() {
        return totalSeats - occupiedSeats; // Возвращаем количество свободных мест
    }

    // Метод для вычисления цены для группы
    public int priceForGroup(int numberOfPeople) {
        return numberOfPeople * pricePerSeat; // Общая стоимость
    }
}

public class TouristBusApp {
    public static void main(String[] args) {
        // Создаем два объекта «Туристический автобус»
        Bus bus1 = new Bus(50, 500); // 50 мест, 500 за место
        Bus bus2 = new Bus(50, 600); // 50 мест, 600 за место

        Scanner scanner = new Scanner(System.in); // Объект для ввода данных

        // Ввод количества людей для первого автобуса
        System.out.print("Введите количество людей для первого автобуса: ");
        int peopleBus1 = scanner.nextInt();

        // Ввод количества людей для второго автобуса
        System.out.print("Введите количество людей для второго автобуса: ");
        int peopleBus2 = scanner.nextInt();

        // Бронирование мест и получение стоимости
        int price1 = bus1.bookSeats(peopleBus1);
        int price2 = bus2.bookSeats(peopleBus2);

        // Вывод информации о первом автобусе
        if (price1 != -1) {
            System.out.println("Свободные места в первом автобусе: " + bus1.availableSeats());
            System.out.println("Цена за поездку для первой группы: " + price1 + " рублей");
        } else {
            System.out.println("Недостаточно мест в первом автобусе.");
        }

        // Вывод информации о втором автобусе
        if (price2 != -1) {
            System.out.println("Свободные места во втором автобусе: " + bus2.availableSeats());
            System.out.println("Цена за поездку для второй группы: " + price2 + " рублей");
        } else {
            System.out.println("Недостаточно мест во втором автобусе.");
        }

        scanner.close(); // Закрываем сканнер
    }
}
        






public class TouristBus {
    private int numberOfSeats;          // Количество мест
    private double pricePerSeat;        // Стоимость одного места
    private int occupiedSeats;          // Количество занятых мест

    // Конструктор по умолчанию
    public TouristBus() {
        this.numberOfSeats = 50;       // Предполагаем 50 мест по умолчанию
        this.pricePerSeat = 100.0;     // Предполагаем стоимость 100
        this.occupiedSeats = 0;         // Изначально автобус пуст
    }

    // Конструктор с параметрами
    public TouristBus(int numberOfSeats, double pricePerSeat) {
        if (numberOfSeats <= 0 || pricePerSeat < 0) {
            throw new IllegalArgumentException("Количество мест должно быть положительным, а цена - ненегативной.");
        }
        this.numberOfSeats = numberOfSeats;
        this.pricePerSeat = pricePerSeat;
        this.occupiedSeats = 0;         // Изначально автобус пуст
    }

    // Конструктор копирования
    public TouristBus(TouristBus bus) {
        this.numberOfSeats = bus.numberOfSeats;
        this.pricePerSeat = bus.pricePerSeat;
        this.occupiedSeats = bus.occupiedSeats;
    }

    // Метод для изменения количества занятых мест
    public void bookSeats(int seats) {
        if (seats <= 0) {
            throw new IllegalArgumentException("Количество бронируемых мест должно быть положительным.");
        }
        if (occupiedSeats + seats <= numberOfSeats) {
            occupiedSeats += seats;
        } else {
            System.out.println("Недостаточно свободных мест.");
        }
    }

    // Метод для получения количества свободных мест
    public int getAvailableSeats() {
        return numberOfSeats - occupiedSeats;
    }

    // Метод для проверки, пуст автобус или заполнен
    public boolean isEmpty() {
        return occupiedSeats == 0;
    }

    public boolean isFull() {
        return occupiedSeats == numberOfSeats;
    }

    // Метод для расчета общей стоимости занятых мест
    public double calculateTotalOccupiedCost() {
        return occupiedSeats * pricePerSeat;
    }

    // Геттеры и сеттеры
    public int getNumberOfSeats() {
        return numberOfSeats;
    }

    public void setNumberOfSeats(int numberOfSeats) {
        if (numberOfSeats <= 0) {
            throw new IllegalArgumentException("Количество мест должно быть положительным.");
        }
        this.numberOfSeats = numberOfSeats;
    }

    public double getPricePerSeat() {
        return pricePerSeat;
    }

    public void setPricePerSeat(double pricePerSeat) {
        if (pricePerSeat < 0) {
            throw new IllegalArgumentException("Цена не может быть отрицательной.");
        }
        this.pricePerSeat = pricePerSeat;
    }

    public int getOccupiedSeats() {
        return occupiedSeats;
    }

    // Метод для вывода информации о автобусе
    public void printInfo() {
        System.out.println("Информация о туристическом автобусе:");
        System.out.println("Количество мест: " + numberOfSeats);
        System.out.println("Стоимость одного места: " + pricePerSeat);
        System.out.println("Занятые места: " + occupiedSeats);
        System.out.println("Свободные места: " + getAvailableSeats());
        System.out.println("Автобус " + (isEmpty() ? "пуст" : "не пуст") + ".");
        System.out.println("Автобус " + (isFull() ? "полон" : "не полон") + ".");
        System.out.println("Общая стоимость занятых мест: " + calculateTotalOccupiedCost());
    }

    // Пример использования класса
    public static void main(String[] args) {
        TouristBus bus = new TouristBus();
        bus.printInfo();

        bus.bookSeats(10);
        bus.printInfo();

        bus.bookSeats(45);  // Попытка забронировать больше мест, чем доступно
        bus.printInfo();
    }
}






public class TouristBus {
    private int numberOfSeats;          // Количество мест
    private double pricePerSeat;        // Стоимость одного места
    private int occupiedSeats;          // Количество занятых мест

    // Конструктор по умолчанию
    public TouristBus() {
        this.numberOfSeats = 50;       // Предполагаем 50 мест по умолчанию
        this.pricePerSeat = 100.0;     // Предполагаем стоимость 100
        this.occupiedSeats = 0;         // Изначально автобус пуст
    }

    // Конструктор с параметрами
    public TouristBus(int numberOfSeats, double pricePerSeat) {
        if (numberOfSeats <= 0 || pricePerSeat < 0) {
            throw new IllegalArgumentException("Количество мест должно быть положительным, а цена - ненегативной.");
        }
        this.numberOfSeats = numberOfSeats;
        this.pricePerSeat = pricePerSeat;
        this.occupiedSeats = 0;         // Изначально автобус пуст
    }

    // Конструктор копирования
    public TouristBus(TouristBus bus) {
        this.numberOfSeats = bus.numberOfSeats;
        this.pricePerSeat = bus.pricePerSeat;
        this.occupiedSeats = bus.occupiedSeats;
    }

    // Метод для изменения количества занятых мест
    public void bookSeats(int seats) {
        if (seats <= 0) {
            throw new IllegalArgumentException("Количество бронируемых мест должно быть положительным.");
        }
        if (occupiedSeats + seats <= numberOfSeats) {
            occupiedSeats += seats;
        } else {
            System.out.println("Недостаточно свободных мест.");
        }
    }

    // Метод для получения количества свободных мест
    public int getAvailableSeats() {
        return numberOfSeats - occupiedSeats;
    }

    // Метод для проверки, пуст автобус или заполнен
    public boolean isEmpty() {
        return occupiedSeats == 0;
    }

    public boolean isFull() {
        return occupiedSeats == numberOfSeats;
    }

    // Метод для расчета общей стоимости занятых мест
    public double calculateTotalOccupiedCost() {
        return occupiedSeats * pricePerSeat;
    }

    // Геттеры и сеттеры
    public int getNumberOfSeats() {
        return numberOfSeats;
    }

    public void setNumberOfSeats(int numberOfSeats) {
        if (numberOfSeats <= 0) {
            throw new IllegalArgumentException("Количество мест должно быть положительным.");
        }
        this.numberOfSeats = numberOfSeats;
    }

    public double getPricePerSeat() {
        return pricePerSeat;
    }

    public void setPricePerSeat(double pricePerSeat) {
        if (pricePerSeat < 0) {
            throw new IllegalArgumentException("Цена не может быть отрицательной.");
        }
        this.pricePerSeat = pricePerSeat;
    }

    public int getOccupiedSeats() {
        return occupiedSeats;
    }
}




public class TouristBus {
    private int numberOfSeats;          // Количество мест
    private double pricePerSeat;        // Стоимость одного места
    private int occupiedSeats;          // Количество занятых мест

    // Конструктор по умолчанию
    public TouristBus() {
        this.numberOfSeats = 50;       // Предполагаем 50 мест по умолчанию
        this.pricePerSeat = 100.0;     // Предполагаем стоимость 100
        this.occupiedSeats = 0;         // Изначально автобус пуст
    }

    // Конструктор с параметрами
    public TouristBus(int numberOfSeats, double pricePerSeat) {
        this.numberOfSeats = numberOfSeats;
        this.pricePerSeat = pricePerSeat;
        this.occupiedSeats = 0;         // Изначально автобус пуст
    }

    // Конструктор копирования
    public TouristBus(TouristBus bus) {
        this.numberOfSeats = bus.numberOfSeats;
        this.pricePerSeat = bus.pricePerSeat;
        this.occupiedSeats = bus.occupiedSeats;
    }

    // Метод для изменения количества занятых мест
    public void bookSeats(int seats) {
        if (occupiedSeats + seats <= numberOfSeats) {
            occupiedSeats += seats;
        } else {
            System.out.println("Недостаточно свободных мест.");
        }
    }

    // Метод для получения количества свободных мест
    public int getAvailableSeats() {
        return numberOfSeats - occupiedSeats;
    }

    // Метод для проверки, пуст автобус или заполнен
    public boolean isEmpty() {
        return occupiedSeats == 0;
    }

    public boolean isFull() {
        return occupiedSeats == numberOfSeats;
    }

    // Метод для расчета общей стоимости занятых мест
    public double calculateTotalOccupiedCost() {
        return occupiedSeats * pricePerSeat;
    }

    // Геттеры и сеттеры
    public int getNumberOfSeats() {
        return numberOfSeats;
    }

    public void setNumberOfSeats(int numberOfSeats) {
        this.numberOfSeats = numberOfSeats;
    }

    public double getPricePerSeat() {
        return pricePerSeat;
    }

    public void setPricePerSeat(double pricePerSeat) {
        this.pricePerSeat = pricePerSeat;
    }

    public int getOccupiedSeats() {
        return occupiedSeats;
    }
}









import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;

public class Product {
    private int id;
    private String name;
    private String description;
    private double price;
    private int stockQuantity;
    private int orderQuantity;
    private int orderId;

    public Product(int id, String name, String description, double price, int stockQuantity) {
        this.id = id;
        this.name = name;
        this.description = description;
        this.price = price;
        this.stockQuantity = stockQuantity;
        this.orderQuantity = 0;
        this.orderId = 0;
    }

    public String toString() {
        ObjectMapper mapper = new ObjectMapper();
        try {
            return mapper.writeValueAsString(this);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return null;
    }

    public void setOrderQuantity(int orderQuantity, int orderId) {
        if (orderQuantity <= stockQuantity) {
            this.orderQuantity = orderQuantity;
            this.orderId = orderId;
        } else {
            System.out.println("Error: Not enough stock quantity");
        }
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getDescription() {
        return description;
    }

    public double getPrice() {
        return price;
    }

    public int getStockQuantity() {
        return stockQuantity;
    }

    public int getOrderQuantity() {
        return orderQuantity;
    }

    public int getOrderId() {
        return orderId;
    }

    public static void main(String[] args) {
        Product product1 = new Product(1, "Product 1", "Description for Product 1", 10.0, 100);
        System.out.println(product1.toString());

        product1.setOrderQuantity(50, 1);

        System.out.println(product1.toString());
    }
}

...
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Product product1 = new Product(1, "Product 1", "Description for Product 1", 10.0, 100);
        Product product2 = new Product(2, "Product 2", "Description for Product 2", 20.0, 50);

        Scanner scanner = new Scanner(System.in);

        System.out.println("Available products:");
        System.out.println(product1.getId() + ": " + product1.getName());
        System.out.println(product2.getId() + ": " + product2.getName());

        System.out.print("Enter product id: ");
        int productId = scanner.nextInt();
        System.out.print("Enter quantity: ");
        int quantity = scanner.nextInt();

        Product selectedProduct = null;
        if (productId == product1.getId()) {
            selectedProduct = product1;
        } else if (productId == product2.getId()) {
            selectedProduct = product2;
        } else {
            System.out.println("Error: Product not found");
            return;
        }

        selectedProduct.setOrderQuantity(quantity, 1);

        System.out.println("Order summary:");
        System.out.println(selectedProduct.toString());
    }
}





csharp
Copy code
import java.util.ArrayList;
import java.util.List;

class Product {
    private int id;
    private String name;
    private String description;
    private double price;
    private int stockQuantity;
    private int orderQuantity;
    private int orderId;

    public Product(int id, String name, String description, double price, int stockQuantity) {
        this.id = id;
        this.name = name;
        this.description = description;
        this.price = price;
        this.stockQuantity = stockQuantity;
        this.orderQuantity = 0;
        this.orderId = -1;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getDescription() {
        return description;
    }

    public double getPrice() {
        return price;
    }

    public int getStockQuantity() {
        return stockQuantity;
    }

    public int getOrderQuantity() {
        return orderQuantity;
    }

    public void setOrderQuantity(int orderQuantity) {
        this.orderQuantity = orderQuantity;
    }

    public int getOrderId() {
        return orderId;
    }

    public void setOrderId(int orderId) {
        this.orderId = orderId;
    }
}

class Order {
    private int id;
    private List<Product> products;

    public Order(int id) {
        this.id = id;
        this.products = new ArrayList<>();
    }

    public int getId() {
        return id;
    }

    public List<Product> getProducts() {
        return products;
    }

    public void addProduct(Product product) {
        this.products.add(product);
    }
}

class InternetShop {
    private List<Product> products;
    private List<Order> orders;
    private int nextOrderId;

    public InternetShop() {
        this.products = new ArrayList<>();
        this.orders = new ArrayList<>();
        this.nextOrderId = 1;
    }

    public void addProduct(Product product) {
        products.add(product);
    }

    public void createOrder() {
        Order order = new Order(nextOrderId);
        orders.add(order);
        nextOrderId++;
    }

    public void addProductToOrder(int orderId, int productId, int quantity) {
        Order order = findOrderById(orderId);
        Product product = findProductById(productId);
        
        if (order != null && product != null && product.getStockQuantity() >= quantity) {
            product.setOrderQuantity(quantity);
            product.setOrderId(orderId);
            order.addProduct(product);
            product.setStockQuantity(product.getStockQuantity() - quantity);
        }
    }

    private Order findOrderById(int orderId) {
        for (Order order : orders) {
            if (order.getId() == orderId) {
                return order;
            }
        }
        return null;
    }

    private Product findProductById(int productId) {
        for (Product product : products) {
            if (product.getId() == productId) {
                return product;
            }
        }
        return null;
    }
}

public class Main {
    public static void main(String[] args) {
        InternetShop shop = new InternetShop();
        
        Product product1 = new Product(1, "Phone", "Smartphone with great features", 500.0, 10);
        Product product2 = new Product(2, "Laptop", "High-performance laptop", 1000.0, 5);
        
        shop.addProduct(product1);
        shop.addProduct(product2);
        
        shop.createOrder();
        shop.addProductToOrder(1, 1, 2);
    }
}





class ArrayBubble{
    private long[] a;   //ссылка на массив
    private int elems;  //количество элементов в массиве

    public ArrayBubble(int max){    //конструктор класса
        a = new long[max];          //создание массива размером max
        elems = 0;                  //при создании массив содержит 0 элементов
    }

    public void into(long value){   //метод вставки элемента в массив
        a[elems] = value;           //вставка value в массив a
        elems++;                    //размер массива увеличивается
    }

    public void printer(){          //метод вывода массива в консоль
        for (int i = 0; i < elems; i++){    //для каждого элемента в массиве
            System.out.print(a[i] + " ");   //вывести в консоль
            System.out.println("");         //с новой строки
        }
        System.out.println("----Окончание вывода массива----");
    }

    private void toSwap(int first, int second){ //метод меняет местами пару чисел массива
        long dummy = a[first];      //во временную переменную помещаем первый элемент
        a[first] = a[second];       //на место первого ставим второй элемент
        a[second] = dummy;          //вместо второго элемента пишем первый из временной памяти
    }

    public void bubbleSorter(){     //МЕТОД ПУЗЫРЬКОВОЙ СОРТИРОВКИ
        for (int out = elems - 1; out >= 1; out--){  //Внешний цикл
            for (int in = 0; in < out; in++){       //Внутренний цикл
                if(a[in] > a[in + 1])               //Если порядок элементов нарушен
                    toSwap(in, in + 1);             //вызвать метод, меняющий местами
            }
        }
    }
}

public class Main {
    public static void main(String[] args) {
        ArrayBubble array = new ArrayBubble(5); //Создаем массив array на 5 элементов

        array.into(163);       //заполняем массив
        array.into(300);
        array.into(184);
        array.into(191);
        array.into(174);

        array.printer();            //выводим элементы до сортировки
        array.bubbleSorter();       //ИСПОЛЬЗУЕМ ПУЗЫРЬКОВУЮ СОРТИРОВКУ
        array.printer();            //снова выводим отсортированный йсписок
    }
}
