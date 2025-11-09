import java.io.*;
import java.util.*;

// Base class representing an item
class Item implements Serializable {
    private String itemName;
    private int quantity;
    private double price;
    private String barcode;

    public Item(String itemName, int quantity, double price, String barcode) {
        this.itemName = itemName;
        this.quantity = quantity;
        this.price = price;
        this.barcode = barcode;
    }

    public String getItemName() { return itemName; }
    public int getQuantity() { return quantity; }
    public double getPrice() { return price; }
    public String getBarcode() { return barcode; }

    public void displayInfo() {
        System.out.println("Item Name : " + itemName);
        System.out.println("Quantity  : " + quantity);
        System.out.println("Price     : " + price);
        System.out.println("Barcode   : " + barcode);
        System.out.println("------------------------------");
    }
}

// Utility class for barcode generation
class BarcodeGenerator {
    private static int counter = 1000;

    public static String generateBarcode() {
        return "WH-" + (counter++) + "-" + System.currentTimeMillis() % 10000;
    }
}

// Manages inventory items
class Inventory {
    private List<Item> items = new ArrayList<>();

    public void addItem(String name, int qty, double price) {
        String barcode = BarcodeGenerator.generateBarcode();
        Item newItem = new Item(name, qty, price, barcode);
        items.add(newItem);
        System.out.println("Item added successfully! Barcode: " + barcode);
    }

    public void displayItems() {
        if (items.isEmpty()) {
            System.out.println("No items in inventory.");
            return;
        }
        for (Item item : items) {
            item.displayInfo();
        }
    }

    public void searchByBarcode(String code) {
        for (Item item : items) {
            if (item.getBarcode().equals(code)) {
                System.out.println("Item Found:");
                item.displayInfo();
                return;
            }
        }
        System.out.println("No item found with that barcode.");
    }

    public void saveToFile(String filename) {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filename))) {
            oos.writeObject(items);
            System.out.println("Inventory saved to file.");
        } catch (IOException e) {
            System.out.println("Error saving file: " + e.getMessage());
        }
    }

    public void loadFromFile(String filename) {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(filename))) {
            items = (List<Item>) ois.readObject();
            System.out.println("Inventory loaded successfully!");
        } catch (Exception e) {
            System.out.println("No existing file or error loading file.");
        }
    }
}

// Main driver class
public class WarehouseApp {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Inventory inv = new Inventory();
        inv.loadFromFile("inventory.dat");

        while (true) {
            System.out.println("\n=== Warehouse Inventory System ===");
            System.out.println("1. Add Item");
            System.out.println("2. Display All Items");
            System.out.println("3. Search by Barcode");
            System.out.println("4. Save & Exit");
            System.out.print("Enter your choice: ");
            int choice = sc.nextInt();
            sc.nextLine(); // consume newline

            switch (choice) {
                case 1:
                    System.out.print("Enter item name: ");
                    String name = sc.nextLine();
                    System.out.print("Enter quantity: ");
                    int qty = sc.nextInt();
                    System.out.print("Enter price: ");
                    double price = sc.nextDouble();
                    inv.addItem(name, qty, price);
                    break;
                case 2:
                    inv.displayItems();
                    break;
                case 3:
                    System.out.print("Enter barcode: ");
                    String code = sc.nextLine();
                    inv.searchByBarcode(code);
                    break;
                case 4:
                    inv.saveToFile("inventory.dat");
                    System.out.println("Exiting program...");
                    return;
                default:
                    System.out.println("Invalid choice!");
            }
        }
    }
}
