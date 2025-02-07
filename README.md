# LeaveManagementSystem
use simple java functions to develops this Leave Management System
import java.util.*;

// Employee Class
class Employee {
    String id;
    String name;
    String password;
    int leaveBalance;

    // Constructor
    Employee(String id, String name, String password, int leaveBalance) {
        this.id = id;
        this.name = name;
        this.password = password;
        this.leaveBalance = leaveBalance;
    }

    // Method to apply for leave
    public void applyLeave(int days) {
        if (leaveBalance >= days) {
            leaveBalance -= days;
            System.out.println("Leave applied for " + days + " days.");
        } else {
            System.out.println("Insufficient leave balance.");
        }
    }

    // Method to view leave balance
    public void viewLeaveBalance() {
        System.out.println("Your available leave balance is: " + leaveBalance + " days.");
    }

    // Main method to test Employee class functionality
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        // Create an Employee object for testing
        Employee emp = new Employee("29", "vedika", "ved123", 20);
        System.out.println("Testing Employee class...");
        System.out.println("Welcome " + emp.name);
        
        // Test applying leave
        System.out.print("Enter number of days you want to apply for leave: ");
        int days = scanner.nextInt();
        emp.applyLeave(days);

        // Test viewing leave balance
        emp.viewLeaveBalance();
    }
}

// LeaveRequest Class
class LeaveRequest {
    String empId;
    String startDate;
    String endDate;
    String status;

    LeaveRequest(String empId, String startDate, String endDate) {
        this.empId = empId;
        this.startDate = startDate;
        this.endDate = endDate;
        this.status = "Pending";
    }

    public void approve() {
        status = "Approved";
        System.out.println("Leave request approved.");
    }

    public void reject() {
        status = "Rejected";
        System.out.println("Leave request rejected.");
    }

    @Override
    public String toString() {
        return "Employee ID: " + empId + ", From: " + startDate + " To: " + endDate + " Status: " + status;
    }
}

// Manager Class
class Manager {
    String id;
    String password;

    Manager(String id, String password) {
        this.id = id;
        this.password = password;
    }

    public void approveRejectLeave(HashMap<Integer, LeaveRequest> leaveRequests) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Enter Leave Request ID to approve/reject:");
        int requestId = scanner.nextInt();
        LeaveRequest request = leaveRequests.get(requestId);
        if (request != null && request.status.equals("Pending")) {
            System.out.println(request);
            System.out.println("Approve or Reject? (A/R): ");
            char choice = scanner.next().charAt(0);
            if (choice == 'A' || choice == 'a') {
                request.approve();
            } else if (choice == 'R' || choice == 'r') {
                request.reject();
            } else {
                System.out.println("Invalid choice.");
            }
        } else {
            System.out.println("Invalid request ID or already processed.");
        }
    }
}

// Main class (LeaveManagementSystemApp)
public class LeaveManagementSystemApp {
    static Scanner scanner = new Scanner(System.in);
    static HashMap<String, Employee> employees = new HashMap<>();
    static HashMap<String, Manager> managers = new HashMap<>();
    static HashMap<Integer, LeaveRequest> leaveRequests = new HashMap<>();
    static int leaveRequestId = 1;

    public static void main(String[] args) {
        // Adding sample data
        employees.put("29", new Employee("29", "vedika", "ved123", 20));
        employees.put("E002", new Employee("E002", "Bob", "emp456", 15));
        managers.put("M001", new Manager("M001", "mgr123"));

        while (true) {
            System.out.println("Welcome to the Leave Management System");
            System.out.println("1. Employee Login");
            System.out.println("2. Manager Login");
            System.out.println("3. Exit");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();

            if (choice == 1) {
                employeeLogin();
            } else if (choice == 2) {
                managerLogin();
            } else if (choice == 3) {
                System.out.println("Exiting...");
                break;
            } else {
                System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    public static void employeeLogin() {
        System.out.print("Enter Employee ID: ");
        String empId = scanner.next();
        System.out.print("Enter Password: ");
        String password = scanner.next();

        if (employees.containsKey(empId) && employees.get(empId).password.equals(password)) {
            employeeMenu(empId);
        } else {
            System.out.println("Invalid credentials.");
        }
    }

    public static void employeeMenu(String empId) {
        while (true) {
            System.out.println("Welcome, " + employees.get(empId).name);
            System.out.println("1. Apply for Leave");
            System.out.println("2. View Leave Balance");
            System.out.println("3. Logout");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();

            if (choice == 1) {
                System.out.print("Enter number of days you want to apply for: ");
                int days = scanner.nextInt();
                employees.get(empId).applyLeave(days);
                if (days <= employees.get(empId).leaveBalance) {
                    System.out.print("Enter start date of leave (YYYY-MM-DD): ");
                    String startDate = scanner.next();
                    System.out.print("Enter end date of leave (YYYY-MM-DD): ");
                    String endDate = scanner.next();
                    leaveRequests.put(leaveRequestId++, new LeaveRequest(empId, startDate, endDate));
                }
            } else if (choice == 2) {
                employees.get(empId).viewLeaveBalance();
            } else if (choice == 3) {
                break;
            } else {
                System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    public static void managerLogin() {
        System.out.print("Enter Manager ID: ");
        String mgrId = scanner.next();
        System.out.print("Enter Password: ");
        String password = scanner.next();

        if (managers.containsKey(mgrId) && managers.get(mgrId).password.equals(password)) {
            managerMenu(mgrId);
        } else {
            System.out.println("Invalid credentials.");
        }
    }

    public static void managerMenu(String mgrId) {
        while (true) {
            System.out.println("Welcome, Manager");
            System.out.println("1. View Pending Leave Requests");
            System.out.println("2. Approve/Reject Leave Request");
            System.out.println("3. Logout");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();

            if (choice == 1) {
                viewPendingRequests();
            } else if (choice == 2) {
                Manager manager = managers.get(mgrId);
                manager.approveRejectLeave(leaveRequests);
            } else if (choice == 3) {
                break;
            } else {
                System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    public static void viewPendingRequests() {
        System.out.println("Pending Leave Requests:");
        for (LeaveRequest request : leaveRequests.values()) {
            if (request.status.equals("Pending")) {
                System.out.println(request);
            }
        }
    }
}
