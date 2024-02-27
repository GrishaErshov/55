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
