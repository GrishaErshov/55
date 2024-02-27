port com.fasterxml.jackson.core.JsonProcessingException;
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
