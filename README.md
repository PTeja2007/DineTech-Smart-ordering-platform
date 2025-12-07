# DineTech-Smart-ordering-platform
import javax.swing.*; // CO5: Uses Extensible API (Swing)
import java.awt.*;
import java.util.LinkedHashMap;// CO6: Collections Framework
import java.io.FileWriter;// CO6: File I/O
import java.io.IOException;// CO6: Exception Handling
import java.text.SimpleDateFormat;
import java.util.Date;
import java.io.File;
import java.util.concurrent.atomic.AtomicInteger;// CO6: Concurrency and Generics

public class DineTechJavaProject { // CO4: Class Definition (OOP Principle), CO5: Potential base for Inheritance

    private JFrame frame;  // CO4: Encapsulation (Instance variable)
    private JTextField nameField, phoneField, tableField; // CO4: Encapsulation
    // JCheckBox saveLoginCheck removed for admin logging purposes
    private JComboBox<String> menuBox;
    private JSpinner qtySpinner;
    private JTextArea cartArea;
    private JButton addToCartBtn, finishOrderBtn;
    private JTextArea billArea;
    private JComboBox<String> paymentBox;

    // Tracking UI Components
    private JTextArea trackingArea;
    private JButton updateStatusBtn;

    // Review components
    private JComboBox<String> ratingBox;
    private JTextArea reviewBox;

    private LinkedHashMap<String, Integer> menu = new LinkedHashMap<>();
    private LinkedHashMap<String, Integer> cart = new LinkedHashMap<>();

    private String username = ""; // CO1: Data Type (String)
    private String userPhone = ""; // CO1: Data Type (String)
    private String tableNumber = "";  // CO1: Data Type (String)
    private int finalTotal = 0; // CO1: Data Type (int)

    // Tracking Variables
    private String orderId = "";
    private String orderStatus = "Placed";
    private final String[] STATUS_CYCLE = {"Placed", "Received by Kitchen", "Being Prepared", "Quality Check", "Ready for Pickup/Serve"}; // CO2: One-dimensional Array
    private AtomicInteger statusIndex = new AtomicInteger(0); // CO6: Concurrency and Generics
    
    // CO4: Abstraction/Modularity through constants
    Color LAVENDER = new Color(230, 230, 250);
    Color BUTTON_COLOR = new Color(180, 140, 250);
    Color WHITE = Color.WHITE;

    // Directory path is designed for admin records, separate from customer interaction
    private String getSavingPath() {
        String tempDir = System.getProperty("java.io.tmpdir");
        File saveDir = new File(tempDir, "DineTech_Data_Admin"); 
        if (!saveDir.exists()) { // CO1: Conditional Statement (if)
            saveDir.mkdirs();
        }
        return saveDir.getAbsolutePath() + File.separator;
    }

    public DineTechJavaProject() {  // CO4: Constructor, CO5: Object
        frame = new JFrame("DineTech - Smart Digital Ordering System");
        frame.setSize(650, 550);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        setupMenu();// CO4: Modularization (Method call)
        // Removed loadSavedCredentials() so customer fields are always blank
        loginPage();

        frame.setVisible(true);
    }

    private void setupMenu() { // CO4: Modularization, CO5: Setting up menu data (model-world entities)
        // CO1: Basic data assignment (Map put)
        menu.put("AI Recommended: Smoked Butter Chicken Biryani - ₹399", 399);
        menu.put("Chef's Special: Signature Lamb Kebab Platter - ₹549", 549);
        menu.put("Veg Delight: Paneer Tikka Masala Wrap - ₹299", 299);
        menu.put("Classic: Hand-Tossed Margherita Pizza - ₹199", 199);
        menu.put("Gourmet: Crispy Zucchini Fries with Aioli - ₹149", 149);
        menu.put("Decadent: Double Chocolate Fudge Brownie - ₹179", 179);
        menu.put("Quench: Sparkling Mint Lemonade - ₹89", 89);
        menu.put("The Mighty Veg Burger - ₹129", 129);
        menu.put("Golden French Fries - ₹99", 99);
        menu.put("Iced Cold Coffee Blast - ₹89", 89);
        menu.put("Velvet Chocolate Shake - ₹149", 149);
        menu.put("Arctic Ice Cream Sundae - ₹129", 129);
        menu.put("Southwest Chicken Quesadillas - ₹349", 349);
        menu.put("Fizzy Mango Mojito - ₹119", 119);
    }

    // Admin Logging: Saves customer login details to a persistent log file.
    private void saveCredentials() { // CO4: Modularization

        String timestamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
        String path = getSavingPath() + "Admin_Login_Log.txt";
        
        try (FileWriter writer = new FileWriter(path, true)) { // 'true' for append mode // CO6: File I/O, Exception Handling (Try-with-resources)

            writer.write("----- Login at " + timestamp + " -----\n");
            writer.write("Customer Name: " + username + "\n");
            writer.write("Phone: " + userPhone + "\n");
            writer.write("Table: " + tableNumber + "\n");
            writer.write("--------------------------------\n");
        } catch (IOException e) {
            System.err.println("Admin log save error: " + e.getMessage());
        }
    }

    private void loginPage() { // CO4: Modularization
        JPanel panel = new JPanel();
        panel.setLayout(null);
        panel.setBackground(LAVENDER);

        JLabel title = new JLabel("DineTech: Simplify Ordering via Smart Technology");
        title.setBounds(100, 20, 500, 30);
        title.setFont(new Font("Serif", Font.BOLD, 24));

        JLabel nameLbl = new JLabel("Enter Name:");
        nameLbl.setBounds(150, 80, 150, 30);
        nameField = new JTextField(); 
        nameField.setBounds(280, 80, 180, 30);

        JLabel phoneLbl = new JLabel("Mobile Number:");
        phoneLbl.setBounds(150, 130, 150, 30);
        phoneField = new JTextField(); 
        phoneField.setBounds(280, 130, 180, 30);

        JLabel tableLbl = new JLabel("Table Number:");
        tableLbl.setBounds(150, 180, 150, 30);
        tableField = new JTextField(); 
        tableField.setBounds(280, 180, 180, 30);

        JButton loginBtn = new JButton("Start Ordering");
        loginBtn.setBounds(230, 270, 160, 40);
        loginBtn.setBackground(BUTTON_COLOR);
        loginBtn.setForeground(WHITE);

        loginBtn.addActionListener(e -> { // CO1: Functional Programming/Action Listener
            username = nameField.getText().trim();// CO1: Operator (assignment)
            userPhone = phoneField.getText().trim();
            tableNumber = tableField.getText().trim();

            if (username.isEmpty() || userPhone == null || userPhone.length() < 10 || tableNumber.isEmpty()) { // CO1: Conditional Logic (if) and Logical Operators
                JOptionPane.showMessageDialog(frame, "Please enter all details correctly!");
            } else {
                // Mandatory Admin Log
                saveCredentials();  // CO1: Logic implementation
                menuPage();
                menuPage();
            }
        });

        panel.add(title);
        panel.add(nameLbl);
        panel.add(nameField);
        panel.add(phoneLbl);
        panel.add(phoneField);
        panel.add(tableLbl);
        panel.add(tableField);
        panel.add(loginBtn);

        frame.setContentPane(panel);
        frame.revalidate();
    }

    private void menuPage() { // CO4: Modularization
        JPanel panel = new JPanel();
        panel.setLayout(null);
        panel.setBackground(LAVENDER);

        JLabel title = new JLabel("Smart Digital Menu Selection");
        title.setFont(new Font("Arial", Font.BOLD, 22));
        title.setBounds(180, 20, 350, 30);
        
        JLabel subtitle = new JLabel("Voice-optimized ordering & AI dish recommendations below:");
        subtitle.setBounds(150, 50, 400, 30);

        menuBox = new JComboBox<>(menu.keySet().toArray(new String[0])); // CO6: Generics
        menuBox.setBounds(180, 90, 250, 30);
        menuBox.setBackground(WHITE);

        qtySpinner = new JSpinner(new SpinnerNumberModel(1, 1, 10, 1));
        qtySpinner.setBounds(440, 90, 50, 30);

        addToCartBtn = new JButton("Add to Cart");
        addToCartBtn.setBounds(260, 140, 120, 35);
        addToCartBtn.setBackground(BUTTON_COLOR);
        addToCartBtn.setForeground(WHITE);

        cartArea = new JTextArea();
        cartArea.setEditable(false);
        cartArea.setBackground(WHITE);
        JScrollPane scroll = new JScrollPane(cartArea);
        scroll.setBounds(150, 200, 350, 150);

        finishOrderBtn = new JButton("Proceed to Digital Bill");
        finishOrderBtn.setBounds(230, 380, 170, 40);
        finishOrderBtn.setBackground(BUTTON_COLOR);
        finishOrderBtn.setForeground(WHITE);

        // Action Listener to add items to the cart and update the display.
        addToCartBtn.addActionListener(e -> {
            String item = (String) menuBox.getSelectedItem();
            int qty = (int) qtySpinner.getValue();
            cart.put(item, cart.getOrDefault(item, 0) + qty);  // CO1: Operator use
            updateCartDisplay();
        });

        finishOrderBtn.addActionListener(e -> {
            if (cart.isEmpty()) { // CO1: Conditional logic
                JOptionPane.showMessageDialog(frame, "Your cart is empty. Please add items before proceeding.", "Cart Empty", JOptionPane.WARNING_MESSAGE);
                return;
            }
            billPage();
        });

        panel.add(title);
        panel.add(subtitle);
        panel.add(menuBox);
        panel.add(qtySpinner);
        panel.add(addToCartBtn);
        panel.add(scroll);
        panel.add(finishOrderBtn);

        frame.setContentPane(panel);
        frame.revalidate();
    }

    private void updateCartDisplay() { // CO4: Modularization
        cartArea.setText(""); 
        for (String item : cart.keySet()) { // CO1: Iterative Statement (for-each loop)
            int qty = cart.get(item);
            cartArea.append(item.split(" - ")[0] + " x " + qty + "\n");
        }
        frame.repaint(); 
    }

    // Admin Logging: Saves the complete order and bill summary to a unique file.
    private void saveBillSummary() {
        String timestamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
        orderId = "DT-" + timestamp.substring(8);  // CO3: String Manipulation/Substring
        String fileName = "Order_" + timestamp + ".txt";
        String path = getSavingPath() + fileName;
        
        try (FileWriter writer = new FileWriter(path)) { // CO6: File I/O, Exception Handling
            writer.write("----- DINETECH DIGITAL ORDER SUMMARY -----\n");
            writer.write("Order ID: " + orderId + "\n");
            writer.write("Order Time: " + timestamp + "\n");
            writer.write("Customer: " + username + "\n");
            writer.write("Table: " + tableNumber + "\n");
            writer.write("Payment Method: " + paymentBox.getSelectedItem() + "\n");
            writer.write("----------------------------------------\n");
            writer.write(billArea.getText());
            writer.write("\n----------------------------------------\n");

            JOptionPane.showMessageDialog(frame, 
                "Order and Billing Summary saved successfully. Your Order ID is: " + orderId, 
                "Save Successful", JOptionPane.INFORMATION_MESSAGE);
        } catch (IOException e) {
            JOptionPane.showMessageDialog(frame, 
                "FILE SAVE ERROR:\nCould not save bill summary to:\n" + path + 
                "\n\nPermissions were denied even in the temporary folder.", 
                "File Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void billPage() { // CO4: Modularization
        JPanel panel = new JPanel();
        panel.setLayout(null);
        panel.setBackground(LAVENDER);

        JLabel title = new JLabel("Bill Summary - Reduce Manual Errors");
        title.setBounds(180, 20, 400, 30);
        title.setFont(new Font("Arial", Font.BOLD, 22));

        billArea = new JTextArea();
        billArea.setEditable(false);
        billArea.setBackground(WHITE);
        JScrollPane scroll = new JScrollPane(billArea);
        scroll.setBounds(150, 100, 350, 200);

        finalTotal = 0;
        billArea.append("Customer: " + username + "\nPhone: " + userPhone + "\nTable: " + tableNumber + "\n-------------------------\n");

        for (String item : cart.keySet()) { // CO1: Iterative Statement
            int qty = cart.get(item);
            int price = menu.get(item);  // CO2: Algorithm design/Data 
            
            finalTotal += qty * price;

            billArea.append(item.split(" - ")[0] + " x " + qty + " = ₹" + (qty * price) + "\n");
        }

        billArea.append("\nTOTAL: ₹" + finalTotal);

        JLabel paymentLbl = new JLabel("Payment Method (Digital Only):");
        paymentLbl.setBounds(150, 320, 200, 30);

        paymentBox = new JComboBox<>(new String[]{"UPI", "Card"});
        paymentBox.setBounds(350, 320, 120, 30);

        JButton payBtn = new JButton("Pay Now & Track Order");
        payBtn.setBounds(230, 370, 200, 40);
        payBtn.setBackground(BUTTON_COLOR);
        payBtn.setForeground(WHITE);

        payBtn.addActionListener(e -> {
            // Mandatorily saves the Bill Summary and Order details
            saveBillSummary(); 
            // Reset status for the new order
            orderStatus = STATUS_CYCLE[0];
            statusIndex.set(0);
            trackOrderPage(); 
        });

        panel.add(title);
        panel.add(scroll);
        panel.add(paymentLbl);
        panel.add(paymentBox);
        panel.add(payBtn);

        frame.setContentPane(panel);
        frame.revalidate();
    }
    
    private void trackOrderPage() { // CO4: Modularization
        JPanel panel = new JPanel();
        panel.setLayout(null);
        panel.setBackground(LAVENDER);

        JLabel title = new JLabel("Live Order Tracking: " + orderId);
        title.setBounds(180, 20, 400, 30);
        title.setFont(new Font("Arial", Font.BOLD, 22));

        JLabel statusLbl = new JLabel("Current Status:");
        statusLbl.setBounds(150, 80, 350, 30);
        statusLbl.setFont(new Font("Arial", Font.PLAIN, 16));
        
        trackingArea = new JTextArea();
        trackingArea.setEditable(false);
        trackingArea.setBackground(WHITE);
        JScrollPane scroll = new JScrollPane(trackingArea);
        scroll.setBounds(150, 120, 350, 200);
        
        updateTrackingArea(); // CO4: Method call
        updateStatusBtn = new JButton("Simulate Kitchen Update (Next Stage)"); 
        updateStatusBtn.setBounds(180, 350, 300, 40);
        updateStatusBtn.setBackground(new Color(255, 165, 0)); 
        updateStatusBtn.setForeground(WHITE);
        
        updateStatusBtn.addActionListener(e -> {
            int nextIndex = statusIndex.incrementAndGet();
            if (nextIndex < STATUS_CYCLE.length) {
                orderStatus = STATUS_CYCLE[nextIndex];
            }
            updateTrackingArea();
            
            if (orderStatus.equals(STATUS_CYCLE[STATUS_CYCLE.length - 1])) {
                updateStatusBtn.setText("Order is Ready!");
                updateStatusBtn.setEnabled(false);
                JOptionPane.showMessageDialog(frame, "Your order is ready! Please proceed to the Review page.", "Order Ready!", JOptionPane.INFORMATION_MESSAGE);
            }
        });

        JButton finishTrackingBtn = new JButton("Proceed to Review");
        finishTrackingBtn.setBounds(230, 420, 200, 40);
        finishTrackingBtn.setBackground(BUTTON_COLOR);
        finishTrackingBtn.setForeground(WHITE);

        finishTrackingBtn.addActionListener(e -> reviewPage());

        panel.add(title);
        panel.add(statusLbl);
        panel.add(scroll);
        panel.add(updateStatusBtn);
        panel.add(finishTrackingBtn);

        frame.setContentPane(panel);
        frame.revalidate();
    }
    
    private void updateTrackingArea() {  // CO4: Modularization
        trackingArea.setText("");
        trackingArea.append("Order ID: " + orderId + "\n");
        trackingArea.append("Table Number: " + tableNumber + "\n");
        trackingArea.append("--------------------------------\n");
        
        for (int i = 0; i < STATUS_CYCLE.length; i++) { // CO1: Iterative Statement (for loop)
            String status = STATUS_CYCLE[i];
            boolean isCurrent = status.equals(orderStatus);
            String marker = isCurrent ? "⭐ CURRENTLY HERE ⭐" : (statusIndex.get() > i ? "✅ Completed" : "⏳ Pending"); // CO1: Ternary Operator
            trackingArea.append(status + ": " + marker + "\n");
        }
        
        frame.repaint(); 
    }
    
    private void reviewPage() {  // CO4: Modularization
        JOptionPane.showMessageDialog(frame, "Payment Successful via " + paymentBox.getSelectedItem() + " ✔\n"
                                     + "Thank you for helping us improve restaurant efficiency and customer satisfaction!");

        JPanel panel = new JPanel();
        panel.setLayout(null);
        panel.setBackground(LAVENDER);

        JLabel title = new JLabel("Rate Your Experience - Promote Digital Transformation");
        title.setBounds(100, 30, 500, 30);
        title.setFont(new Font("Arial", Font.BOLD, 20));

        ratingBox = new JComboBox<>(new String[]{"⭐", "⭐⭐", "⭐⭐⭐", "⭐⭐⭐⭐", "⭐⭐⭐⭐⭐"});
        ratingBox.setBounds(250, 100, 120, 30);

        reviewBox = new JTextArea();
        reviewBox.setBorder(BorderFactory.createTitledBorder("Write Feedback (Helps us improve):"));
        reviewBox.setBounds(170, 150, 300, 150);
        reviewBox.setBackground(WHITE);

        JButton submitBtn = new JButton("Submit");
        submitBtn.setBounds(250, 320, 150, 40);
        submitBtn.setBackground(BUTTON_COLOR);
        submitBtn.setForeground(WHITE);

        submitBtn.addActionListener(e -> {
            JOptionPane.showMessageDialog(frame, "Thank you for your feedback! DineTech wishes you a great day. 💗");
            System.exit(0);
        });

        panel.add(title);
        panel.add(ratingBox);
        panel.add(reviewBox);
        panel.add(submitBtn);

        frame.setContentPane(panel);
        frame.revalidate();
    }


    // ---------- MAIN METHOD ----------
    public static void main(String[] args) {
        new DineTechJavaProject();  // CO4: Object instantiation, CO5: OOP Architecture
    }
}
