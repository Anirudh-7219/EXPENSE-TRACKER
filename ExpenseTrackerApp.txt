package ExpenseTrackerApplication;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

class Expense {
    private String description;
    private double amount;
    private Date date;

    public Expense(String description, double amount) {
        this.description = description;
        this.amount = amount;
        this.date = new Date();
    }

    public String getDescription() {
        return description;
    }

    public double getAmount() {
        return amount;
    }

    public Date getDate() {
        return date;
    }
}

public class ExpenseTrackerApp extends JFrame {
    private List<Expense> expenses;
    private JTextArea expenseTextArea;
    private JTextField descriptionTextField;
    private JTextField amountTextField;

    public ExpenseTrackerApp() {
        setTitle("Expense Tracker");
        setSize(400, 350);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        expenses = new ArrayList<>();
        JPanel panel = new JPanel();
        add(panel);
        placeComponents(panel);
        setVisible(true);
    }

    private void placeComponents(JPanel panel) {
        panel.setLayout(null);

        // Title Label
        JLabel titleLabel = new JLabel("Expense Tracker");
        titleLabel.setFont(new Font("Arial", Font.BOLD, 16));
        titleLabel.setBounds(150, 10, 150, 25);
        panel.add(titleLabel);

        // Description Label and Text Field
        JLabel descriptionLabel = new JLabel("Description:");
        descriptionLabel.setBounds(20, 40, 80, 25);
        panel.add(descriptionLabel);

        descriptionTextField = new JTextField();
        descriptionTextField.setBounds(120, 40, 200, 25);
        panel.add(descriptionTextField);

        // Amount Label and Text Field
        JLabel amountLabel = new JLabel("Amount:");
        amountLabel.setBounds(20, 70, 80, 25);
        panel.add(amountLabel);

        amountTextField = new JTextField();
        amountTextField.setBounds(120, 70, 100, 25);
        panel.add(amountTextField);

        // Add Expense Button
        JButton addButton = new JButton("Add Expense");
        addButton.setBounds(240, 70, 120, 25);
        panel.add(addButton);

        // Expense List Display Area
        expenseTextArea = new JTextArea();
        expenseTextArea.setBounds(20, 110, 340, 100);
        expenseTextArea.setEditable(false);
        panel.add(expenseTextArea);

        // Save Expenses Button
        JButton saveButton = new JButton("Save Expenses");
        saveButton.setBounds(20, 220, 150, 25);
        panel.add(saveButton);

        // Clear Button
        JButton clearButton = new JButton("Clear Fields");
        clearButton.setBounds(180, 220, 150, 25);
        panel.add(clearButton);

        // Action Listeners
        addButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                addExpense();
            }
        });

        saveButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                saveExpensesToFile();
            }
        });

        clearButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                clearInputFields();
            }
        });
    }

    private void addExpense() {
        try {
            String description = descriptionTextField.getText();
            String amountText = amountTextField.getText();

            // Validate inputs
            if (description.isEmpty()) {
                JOptionPane.showMessageDialog(this, "Please enter a description.");
                return;
            }

            double amount = Double.parseDouble(amountText);
            if (amount <= 0) {
                JOptionPane.showMessageDialog(this, "Amount must be greater than zero.");
                return;
            }

            Expense expense = new Expense(description, amount);
            expenses.add(expense);
            updateExpenseTextArea();
            clearInputFields();

        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(this, "Invalid amount. Please enter a numeric value.");
        }
    }

    private void updateExpenseTextArea() {
        SimpleDateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
        StringBuilder sb = new StringBuilder("Expenses:\n");
        for (Expense expense : expenses) {
            sb.append(dateFormat.format(expense.getDate()))
              .append(" - ").append(expense.getDescription())
              .append(": $").append(String.format("%.2f", expense.getAmount()))
              .append("\n");
        }
        expenseTextArea.setText(sb.toString());
    }

    private void clearInputFields() {
        descriptionTextField.setText("");
        amountTextField.setText("");
    }

    private void saveExpensesToFile() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter("expenses.txt", true))) {
            SimpleDateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
            for (Expense expense : expenses) {
                writer.write(dateFormat.format(expense.getDate()) + " - " +
                             expense.getDescription() + ": $" +
                             String.format("%.2f", expense.getAmount()) + "\n");
            }
            JOptionPane.showMessageDialog(null, "Expenses saved to 'expenses.txt'");
        } catch (IOException e) {
            JOptionPane.showMessageDialog(null, "Error saving expenses to file.");
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                new ExpenseTrackerApp();
            }
        });
    }
}
