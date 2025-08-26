
## **Lab Name : Object Oriented Programming Sessional**
## **Submission Date : August 26, 2025**
### **Group Members :**
| Name             | Roll        |
|------------------|-------------|
| MD. NAHID REZA   | 2310037     |
| MD. SHUVRA HASAN | 2310040     |
| MD. ARIFUL ISLAM | 2310044     |

---

<h2>1. Project Title</h2>
<p><strong>E-Ticket Booking System</strong></p>

<h2>2. Project Summary</h2>
<p>A C++-based console application for booking bus, train, and flight tickets, featuring route selection, premium options, fare calculation with discounts, passenger information management, multiple payment methods (Bkash, Nagad, Bank), and file-based storage for ticket details.</p>



### **Code:**

```C++
// Including standard libraries for I/O, file handling, and string manipulation
#include <bits/stdc++.h>
#include <fstream>
#include <sstream>
#include <iomanip>
#include <string>
using namespace std;

// Function declarations for ticket generation and management
string generateTicketID(); // Generates unique ticket ID
int isPremium(int ticketChoice); // Checks if ticket is premium
void buyTicket(); // Handles ticket purchase process
void displayBuyerDetailsByTicketID(const string &searchTicketID); // Displays buyer details by ticket ID

// Template function to round up numbers
template <typename T>
T roundUp(T num, T multiple = 5); // Rounds up number to nearest multiple

// Passenger class to store passenger information
class Passenger {
private:
    string name; // Passenger's name
    int age; // Passenger's age
    string contact; // Passenger's contact number

public:
    Passenger(); // Default constructor
    int getAge(); // Getter for age
    void setInfo(); // Sets passenger information
    void displayInfo(); // Displays passenger information
    string getName() const { return name; }; // Getter for name

    friend class FileSystem; // Allows FileSystem to access private fields
};

// Abstract Ticket class for different ticket types
class Ticket {
protected:
    static int totalTicketsSold; // Tracks total tickets sold
    int ticketType; // 1: Bus, 2: Train, 3: Flight
    int isPremium; // 1: Premium, 0: Regular
    string ticketID; // Unique ticket identifier
    string selectedRoute; // Selected travel route
    int selectedDistance; // Distance of selected route
    string departureTime; // Departure time
    string routes[7] = {
        "Rajshahi - Dhaka",
        "Rajshahi - Khulna",
        "Rajshahi - Rangpur",
        "Rajshahi - Kushtia",
        "Rajshahi - Barisal",
        "Rajshahi - Bogura",
        "Rajshahi - Nator"
    }; // Available routes
    int distance[7] = {280, 250, 200, 90, 300, 150, 55}; // Distances for routes
    int fare; // Ticket fare

public:
    Ticket(int type, int premium); // Constructor with type and premium status
    void applyDiscount(double percentage); // Applies percentage discount
    void applyDiscount(int amount); // Applies fixed amount discount
    void checkDiscount(string coupon); // Checks and applies coupon code
    virtual int calculateFare() = 0; // Pure virtual function for fare calculation
    virtual string setTime() = 0; // Pure virtual function for setting time
    void selectRoute(); // Allows user to select route
    void showTicketInfo(); // Displays ticket information
    int getFare() const { return fare; } // Getter for fare

    friend class FileSystem; // Allows FileSystem to access protected fields
};

// BusTicket class derived from Ticket
class BusTicket : public Ticket {
public:
    BusTicket(int premium); // Constructor
    int calculateFare() override; // Calculates fare for bus
    string setTime() override; // Sets departure time for bus
};

// TrainTicket class derived from Ticket
class TrainTicket : public Ticket {
public:
    TrainTicket(int premium); // Constructor
    int calculateFare() override; // Calculates fare for train
    string setTime() override; // Sets departure time for train
};

// FlightTicket class derived from Ticket
class FlightTicket : public Ticket {
public:
    FlightTicket(int premium); // Constructor
    int calculateFare() override; // Calculates fare for flight
    string setTime() override; // Sets departure time for flight
};

// FileSystem class for handling file operations
class FileSystem {
public:
    void storeTicketInformation(Ticket* ticket, Passenger& passenger); // Stores ticket and passenger info
};

// Implementation of roundUp template function
template <typename T>
T roundUp(T num, T multiple) {
    return ceil(num / multiple) * multiple; // Rounds up to nearest multiple
}

// Passenger class implementation
Passenger::Passenger() {
    name = ""; // Initializes name
    age = 0; // Initializes age
    contact = ""; // Initializes contact
}

int Passenger::getAge() {
    return age; // Returns passenger's age
}

void Passenger::setInfo() {
    while(true)
    {
         try {

            cout << "Enter passenger name: ";
            cin.ignore(); // Clears input buffer
            getline(cin, name); // Gets full name
                if(cin.fail())
                {
                    cin.clear();
                    throw "invalid name!Enter correct one.";
                }
            break;
         }

         catch (const char *e) {
        cout << "Error: " << e << endl;

      }
    }

while (true)
{
    try{
        cout << "Enter passenger age: ";
        cin >> age; // Gets age
        if (age <= 0 || age > 120) {
            cin.clear();
            throw "Invalid age. Must be between 1 and 120."; // Validates age
        }
        break;
    }
     catch (const char *e) {
        cout << "Error: " << e << endl;
        cout << "Please re-enter passenger details correctly.\n";

    }

}

while (true)
{
    try{
        cout << "Enter passenger contact: ";
        cin >> contact; // Gets contact number
        if (contact.size() != 11 || contact.substr(0, 2) != "01") {
            throw "Invalid mobile number. Must start with 01 and be 11 digits."; // Validates contact
        }
        break;
    }
    catch (const char *e) {
        cout << "Error: " << e << endl;
        cout << "Please re-enter passenger details correctly.\n";
    }


}



}

void Passenger::displayInfo() {
    cout << "Passenger Name: " << name << endl;
    cout << "Passenger Age: " << age << endl;
    cout << "Passenger Contact: " << contact << endl; // Displays passenger details
}

// Ticket class static member initialization
int Ticket::totalTicketsSold = 0;

Ticket::Ticket(int type, int premium) {
    ticketType = type; // Sets ticket type
    isPremium = premium; // Sets premium status
}

void Ticket::applyDiscount(double percentage) {
    fare -= (int)(fare * percentage / 100.0); // Applies percentage discount
}

void Ticket::applyDiscount(int amount) {
    fare -= amount; // Applies fixed amount discount
}

void Ticket::checkDiscount(string coupon) {
    if (coupon == "ECE") {
        cout << "You have got 11.11% discount!" << endl;
        applyDiscount((double)11.11); // Applies ECE coupon discount
    } else if (coupon == "first") {
        cout << "First customer get flat 50 taka discount!" << endl;
        applyDiscount(50); // Applies first customer discount
    } else {
        cout << "Invalid coupon code." << endl; // Handles invalid coupon
    }
}

void Ticket::selectRoute() {
    int len;
    if (ticketType == 1) {
        ticketID = "BU" + generateTicketID(); // Generates bus ticket ID
        len = 7; // Sets number of routes for bus
    } else if (ticketType == 2) {
        ticketID = "TR" + generateTicketID(); // Generates train ticket ID
        len = 4; // Sets number of routes for train
    } else if (ticketType == 3) {
        ticketID = "FL" + generateTicketID(); // Generates flight ticket ID
        len = 1; // Sets number of routes for flight
    } else {
        cout << "Invalid ticket type." << endl;
        return; // Handles invalid ticket type
    }

    cout << "\nSelect a route:" << endl;
    for (int i = 0; i < len; i++) {
        cout << i + 1 << ". " << routes[i] << endl; // Displays available routes
    }

    int routeChoice;
    while(true)
    {
        cout << "Enter your choice (1-" << len << "): ";
        cin >> routeChoice; // Gets route choice

    try {
        if (routeChoice < 1 || routeChoice > len) {
            cin.clear();
            throw "Invalid route selection !Select correct one."; // Validates route choice
        }
        selectedRoute = routes[routeChoice - 1]; // Sets selected route
        selectedDistance = distance[routeChoice - 1]; // Sets route distance
        break;
    }
    catch (const char* e) {
        cout << "Error: " << e << endl;

    }

    }

    cout << endl;
    departureTime = setTime(); // Sets departure time
    cout << endl;
}

void Ticket::showTicketInfo() {
    cout << "==========================================================" << endl;
    cout << "Ticket ID: " << ticketID << endl;
    cout << "Selected Route: " << selectedRoute << endl;
    cout << "Ticket Type: " << (ticketType == 1 ? "Bus" : ticketType == 2 ? "Train" : "Flight") << endl;
    cout << "Departure Time: " << departureTime << endl;
    cout << "Is Premium: " << (isPremium ? "Yes" : "No") << endl; // Displays ticket details

    float addPremiumFare = 1;
    if (isPremium) addPremiumFare = 1.5; // Applies premium fare multiplier
    fare = (calculateFare() * addPremiumFare); // Calculates total fare
    fare = roundUp(fare-5); // Rounds up fare
    cout << "Fare: " << fare << endl; // Displays rounded fare

    cout << "\nDo you have any Coupon Code? (1. Yes  --- 2. No): ";
    int hasCoupon;
    cin >> hasCoupon; // Checks for coupon
    if (hasCoupon == 1) {
        string couponCode;
        cout << "Enter coupon code: ";
        cin >> couponCode; // Gets coupon code
        checkDiscount(couponCode); // Applies discount if valid
    }
    if (totalTicketsSold == 0) {
        checkDiscount("first"); // Applies first customer discount
    }
    fare = roundUp(fare); // Rounds up final fare
    cout << "Final Fare: " << fare << endl; // Displays final fare
    cout << "==========================================================\n" << endl;
    totalTicketsSold++; // Increments ticket counter
}

// BusTicket class implementation
BusTicket::BusTicket(int premium) : Ticket(1, premium) {} // Constructor

int BusTicket::calculateFare() {
    return selectedDistance * 3; // Calculates bus fare
}

string BusTicket::setTime() {
    cout << "Select your departure time (1-6): " << endl;
    cout << "1. 08:00 AM" << endl;
    cout << "2. 09:00 AM" << endl;
    cout << "3. 12:00 PM" << endl;
    cout << "4. 03:00 PM" << endl;
    cout << "5. 04:00 PM" << endl;
    cout << "6. 07:00 PM" << endl;
    int timeChoice;
    bool ok=true;
    while(ok)
    {

        cout << "Enter your choice: ";
        cin >> timeChoice; // Gets time choice
         try {
            if(timeChoice<1 || timeChoice>6)
            {
                cin.clear();// reset error
                throw "invalid time ! Enter correct one.";
            }

            switch (timeChoice) {
             case 1: return "08:00 AM";
            case 2: return "09:00 AM";
            case 3: return "12:00 PM";
            case 4: return "03:00 PM";
            case 5: return "04:00 PM";
            case 6: return "07:00 PM";
            }

            break;
        }
        catch (const char* e) {
            cout << "Error: " << e << endl;

        }

   }
}

// TrainTicket class implementation
TrainTicket::TrainTicket(int premium) : Ticket(2, premium) {} // Constructor

int TrainTicket::calculateFare() {
    return selectedDistance * 2; // Calculates train fare
}

string TrainTicket::setTime() {

        cout << "Select your departure time : (1-3)" << endl;
        cout << "1. 08:00 AM" << endl;
        cout << "2. 12:00 PM" << endl;
        cout << "3. 04:00 PM" << endl;
        int timeChoice;


    bool ok=true;
    while(ok)
    {

        cout << "Enter your choice: ";
        cin >> timeChoice; // Gets time choice
         try {
            if(timeChoice!=1 && timeChoice !=2 && timeChoice !=3)
            {
                cin.clear();// reset error
                throw "invalid time ! Enter correct one.";
            }

            switch (timeChoice) {
                case 1: return "08:00 AM";
                case 2: return "12:00 PM";
                case 3: return "04:00 PM";
            }

            break;
        }
        catch (const char* e) {
            cout << "Error: " << e << endl;

        }

   }
}

// FlightTicket class implementation
FlightTicket::FlightTicket(int premium) : Ticket(3, premium) {} // Constructor

int FlightTicket::calculateFare() {
    return 5500; // Fixed fare for flight
}

string FlightTicket::setTime() {
    cout << "Select your departure time : (1-2)" << endl;
    cout << "1. 10:00 AM" << endl;
    cout << "2. 08:00 PM" << endl;
    int timeChoice;

    bool ok=true;
    while(ok)
    {

         cout << "Enter your choice: ";
        cin >> timeChoice; // Gets time choice
         try {
            if(timeChoice!=1 && timeChoice !=2)
            {
                cin.clear();// reset error
                throw "invalid time ! Enter correct one.";
            }

        switch (timeChoice) {
            case 1: return "10:00 AM";
            case 2: return "08:00 PM";
        }

        break;
     }
    catch (const char* e) {
        cout << "Error: " << e << endl;

    }
    }

}

// Generates random ticket ID
string generateTicketID() {
    const string chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    string result;
    for (int i = 0; i < 5; ++i) {
        result += chars[rand() % chars.size()]; // Builds 5-character ID
    }
    return result;
}

// Checks if user wants premium ticket
int isPremium(int ticketChoice) {
    (void)ticketChoice; // Unused parameter
    cout << "Do you want a Premium ticket? (1: Yes, 2: No): ";
    int premiumChoice;
    cin >> premiumChoice; // Gets premium choice
    if (premiumChoice == 1) {
        return 1; // Premium ticket
    }
    return 0; // Regular ticket
}

// Stores ticket and passenger info in file
void FileSystem::storeTicketInformation(Ticket* ticket, Passenger& passenger) {
    ofstream fout("buyer_details.txt", ios::app); // Opens file in append mode
    if (!fout) {
        cout << "Error opening file!" << endl;
        return; // Handles file open error
    }

    fout << passenger.name << ","
         << passenger.age << ","
         << passenger.contact << ","
         << ticket->ticketID << ","
         << ticket->selectedRoute << ","
         << ticket->departureTime << ","
         << (ticket->isPremium ? "Premium" : "Regular") << ","
         << roundUp(ticket->fare) << "\n"; // Writes ticket info to file

    fout.close(); // Closes file
    cout << "Ticket verified and saved successfully!\n" << endl;
}

// Displays buyer details by ticket ID
void displayBuyerDetailsByTicketID(const string &searchTicketID) {
    ifstream fin("buyer_details.txt"); // Opens file for reading
    if (!fin) {
        cout << "No buyer details found.\n";
        return; // Handles file open error
    }

    string line;
    bool found = false;
    const int boxWidth = 50; // Box width for display

    while (getline(fin, line)) {
        stringstream ss(line); // Parses line

        string name, age, contact, ticketID, route, time, type, fare;
        getline(ss, name, ','); // Gets name
        getline(ss, age, ','); // Gets age
        getline(ss, contact, ','); // Gets contact
        getline(ss, ticketID, ','); // Gets ticket ID
        getline(ss, route, ','); // Gets route
        getline(ss, time, ','); // Gets time
        getline(ss, type, ','); // Gets ticket type
        getline(ss, fare, ','); // Gets fare

        if (ticketID == searchTicketID) {
            found = true;

            string top = "=================================================="; // Top border
            string bottom = "==================================================\n"; // Bottom border
            cout <<  endl;
            cout << top << "\n";
            cout << "|                  BUYER DETAILS                 |\n"; // Header
            cout << top << "\n";

            cout << "| Name        : " << setw(33) << left << name << "|\n";
            cout << "| Age         : " << setw(33) << left << age << "|\n";
            cout << "| Contact     : " << setw(33) << left << contact << "|\n";
            cout << "| TicketID    : " << setw(33) << left << ticketID << "|\n";
            cout << "| Route       : " << setw(33) << left << route << "|\n";
            cout << "| Time        : " << setw(33) << left << time << "|\n";
            cout << "| Ticket Type : " << setw(33) << left << type << "|\n";
            cout << "| Total Fare  : " << setw(33) << left << fare + string(" BDT") << "|\n";

            cout << bottom << "\n";
            break; // Exits loop after finding ticket
        }
    }

    if (!found)
        cout << "No details found for TicketID: " << searchTicketID << endl; // Handles ticket not found

    fin.close(); // Closes file
}

// Payment class hierarchy for handling payments
class Payment {
protected:
    double amount; // Payment amount
    string pin;
public:
    Payment(double amt = 0.0) : amount(amt) {} // Constructor
    void processPayment(){
        cout << " Processing Payment of " << amount << " BDT..." << endl;
        cout<< " Transaction id: " << getTransactionID() << endl;

    }
    string getTransactionID() const {
        return "TXN" + to_string(rand() % 10000); // Generates a random transaction ID
    }
    virtual ~Payment() {} // Virtual destructor
};

// Bkash payment class
class Bkash : virtual public Payment {
public:
    Bkash(double amt = 0.0) : Payment(amt) {} // Constructor
    void BkashTransaction() {
        cout << " Starting Bkash Transaction..." << endl;
        cout <<" Enter Bkash pin: ";
        cin>>pin;
        cout << " Bkash Transaction Completed!" << endl;
    }
};

// Nagad payment class
class Nagad : virtual public Payment {
public:
    Nagad(double amt = 0.0) : Payment(amt) {} // Constructor
    void NagadTransaction() {
        cout << " Starting Nagad Transaction..." << endl;
        cout << " Enter Nagad pin: ";
        cin>>pin;
        cout << " Nagad Transaction Completed!" << endl;
    }
};

// Bank payment class
class Bank : virtual public Payment {
public:
    Bank(double amt = 0.0) : Payment(amt) {} // Constructor
    void BankTransaction() {
        cout << " Starting Bank Transaction..." << endl;
        cout << " Enter account number: ";
        cin >> pin;
        cout << " Bank Transaction Completed!" << endl;
    }
};

// PassengerPayment class to handle payment methods
class PassengerPayment : public Bkash, public Nagad, public Bank {
    string passengerName; // Passenger's name
public:
    PassengerPayment(string name, double amt)
        : Payment(amt), Bkash(0), Nagad(0), Bank(0), passengerName(name) {} // Constructor

    void makePayment() {
        cout << "\n=========== PAYMENT GATEWAY ===========" << endl;
        cout << "Passenger: " << passengerName << endl;
        cout << "Total Amount: " << amount << " BDT" << endl;
        cout << "Choose Payment Method:" << endl;
        cout << "1. Bkash\n2. Nagad\n3. Bank" << endl;
        cout << "Enter choice: ";
        int choice;
        cin >> choice; // Gets payment method choice

        cout << "\n---------------------------------------" << endl;
        PassengerPayment::processPayment(); //Daimond Problem Issue and Solve
        switch (choice) {
            case 1: PassengerPayment::BkashTransaction(); break;// Processes Bkash payment
            case 2: PassengerPayment::NagadTransaction(); break; // Processes Nagad payment
            case 3: PassengerPayment::BankTransaction(); break; // Processes Bank payment
            default: cout << " Invalid choice!" << endl; return; // Handles invalid choice
        }
        cout << " Payment Completed Successfully!" << endl;
        cout << "=======================================\n" << endl;
    }
};

// Handles ticket purchase process
void buyTicket() {
    Ticket* ticket = nullptr; // Pointer for ticket object
         cout << "\nWhich type of ticket you want to buy?" << endl;
        cout << "1. Bus Ticket" << endl;
        cout << "2. Train Ticket" << endl;
        cout << "3. Flight Ticket" << endl;
    int ticketChoice;
    while(true)
    {

        cout << "Enter your choice: ";
        cin >> ticketChoice; // Gets ticket type choice
        try{
            if(ticketChoice<1 || ticketChoice>3)
            {
                cin.clear();
                throw "Invalid choice! enter correct one.";
            }
            switch (ticketChoice) {
            case 1:
                cout << "You have selected Bus Ticket.\n" << endl;
                ticket = new BusTicket(isPremium(ticketChoice)); // Creates bus ticket
                ticket->selectRoute(); // Selects route
                break;
            case 2:
                cout << "You have selected Train Ticket.\n" << endl;
                ticket = new TrainTicket(isPremium(ticketChoice)); // Creates train ticket
                ticket->selectRoute(); // Selects route
                break;
            case 3:
                cout << "You have selected Flight Ticket.\n" << endl;
                ticket = new FlightTicket(isPremium(ticketChoice)); // Creates flight ticket
                ticket->selectRoute(); // Selects route
                break;

                }
                break;
        }
        catch(const char* e)
        {
            cout<<"error"<<e<<endl;
        }


    }


    Passenger passenger; // Creates passenger object
    passenger.setInfo(); // Sets passenger info

    cout << "\n=================== Ticket Information ===================" << endl;
    passenger.displayInfo(); // Displays passenger info
    ticket->showTicketInfo(); // Displays ticket info
    PassengerPayment payment(passenger.getName(), ticket->getFare()); // Creates payment object

    payment.makePayment(); // Processes payment

    FileSystem inspector; // Creates FileSystem object
    inspector.storeTicketInformation(ticket, passenger); // Stores ticket info

    delete ticket; // Frees ticket memory
}

// Main function with menu-driven interface
int main() {
    while (1) {
        cout << "1. Buy Ticket" << endl;
        cout << "2. Check Ticket" << endl;
        cout << "To exit enter 0" << endl;
        cout << "Enter your choice: ";
        int choice;
        cin >> choice; // Gets user choice

        if (choice == 1) {
            generateTicketID(); //To intialize randomness
            buyTicket(); // Calls buyTicket function
        } else if (choice == 2) {
            string searchID;
            cout << "Enter TicketID: ";
            cin >> searchID; // Gets ticket ID to search
            displayBuyerDetailsByTicketID(searchID); // Displays ticket details
        } else {
            break; // Exits loop
        }
    }
    return 0; // Exits program
}
```

<h2>3. Program Functionality</h2>
<p>The <strong>Ticket Booking System</strong> is a console-based C++ application designed to streamline ticket reservations for various travel modes. Its key functionalities include:</p>
<ul>
    <li>Allowing users to select ticket types (bus, train, or flight), routes, and premium or regular options.</li>
    <li>Calculating fares based on distance (bus/train) or fixed rates (flight), with discounts via coupon codes or first-customer offers.</li>
    <li>Managing passenger details (name, age, contact) with input validation.</li>
    <li>Processing payments through Bkash, Nagad, or Bank with transaction ID generation.</li>
    <li>Storing ticket and passenger information in a file and retrieving details by ticket ID.</li>
    <li>Providing a menu-driven interface for buying or checking tickets.</li>
</ul>
<p>The system leverages object-oriented programming (OOP) principles for modularity, extensibility, and maintainability.</p>

<h2>4. Implementation of OOP Concepts</h2>

<h3>4.1 Encapsulation</h3>
<p><strong>Definition</strong>: Bundles data and methods into classes, restricting access using private/protected specifiers and exposing functionality via public methods.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>Passenger</code> Class</strong>: Private data (<code>name</code>, <code>age</code>, <code>contact</code>) with public methods (<code>setInfo()</code>, <code>displayInfo()</code>, <code>getAge()</code>, <code>getName()</code>). Location: Lines 59–79, 128–177.</li>
    <li><strong><code>Ticket</code> Class</strong>: Protected data (<code>ticketType</code>, <code>fare</code>, etc.) with public methods (<code>selectRoute()</code>, <code>showTicketInfo()</code>). Location: Lines 81–110, 193–263.</li>
    <li><strong>Purpose</strong>: Ensures data integrity (e.g., validates age/contact) and restricts direct access.</li>
</ul>

<h3>4.2 Inheritance</h3>
<p><strong>Definition</strong>: Derived classes inherit properties/methods from a base class for code reuse.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Base Class</strong>: <code>Ticket</code> (Lines 81–110) with common attributes/methods.</li>
    <li><strong>Derived Classes</strong>: <code>BusTicket</code>, <code>TrainTicket</code>, <code>FlightTicket</code> (Lines 112–131) override <code>calculateFare()</code> and <code>setTime()</code>.</li>
    <li><strong>Usage</strong>: <code>buyTicket()</code> (Lines 512–548) creates derived class objects via <code>Ticket*</code>.</li>
    <li><strong>Purpose</strong>: Shares common functionality while allowing specialized logic.</li>
</ul>

<h3>4.3 Polymorphism</h3>
<p><strong>Definition</strong>: Enables methods to behave differently based on the object. Includes compile-time (overloading) and run-time (virtual functions) polymorphism.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Compile-Time (Function Overloading)</strong>: <code>Ticket::applyDiscount</code> overloaded for percentage (Line 195) and fixed amount (Line 199). Called in <code>checkDiscount()</code> (Line 204).</li>
    <li><strong>Run-Time (Virtual Functions)</strong>: Pure virtual functions <code>calculateFare()</code> and <code>setTime()</code> in <code>Ticket</code> (Lines 104–105), implemented in derived classes (Lines 267, 302, 349, 269–294, 304–326, 351–372). Used via <code>Ticket*</code> in <code>buyTicket()</code> (Lines 512–548).</li>
    <li><strong>Purpose</strong>: Supports flexible discount application and dynamic method resolution.</li>
</ul>

<h3>4.4 Abstraction</h3>
<p><strong>Definition</strong>: Hides implementation details, exposing only essential functionality via abstract classes.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Abstract Class</strong>: <code>Ticket</code> with pure virtual functions <code>calculateFare()</code> and <code>setTime()</code> (Lines 104–105).</li>
    <li><strong>Usage</strong>: Implemented in <code>BusTicket</code>, <code>TrainTicket</code>, <code>FlightTicket</code> (Lines 267, 302, 349, 269–294, 304–326, 351–372).</li>
    <li><strong>Purpose</strong>: Ensures each ticket type defines its own logic while maintaining a common interface.</li>
</ul>

<h3>4.5 Diamond Problem</h3>
<p><strong>Definition</strong>: Occurs in multiple inheritance with a shared base class, resolved using virtual inheritance.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Setup</strong>: <code>Payment</code> (Lines 407–416) is inherited by <code>Bkash</code>, <code>Nagad</code>, <code>Bank</code> (Lines 418–436), which are inherited by <code>PassengerPayment</code> (Lines 438–458).</li>
    <li><strong>Solution</strong>: Virtual inheritance in <code>Bkash</code>, <code>Nagad</code>, <code>Bank</code> (Lines 418, 425, 432) ensures one <code>Payment</code> instance. Used in <code>makePayment()</code> (Line 454).</li>
    <li><strong>Purpose</strong>: Avoids ambiguity in accessing <code>Payment</code> members.</li>
</ul>

<h3>4.6 Error Handling</h3>
<p><strong>Definition</strong>: Uses try–catch blocks within while loops to manage exceptions robustly.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>Passenger::setInfo()</code></strong>: Uses separate <code>while</code> loops to validate <code>name</code> (via <code>cin.fail()</code>), <code>age</code> (1–120), and <code>contact</code> (11 digits, starts with "01") with <code>try-catch</code> blocks (Lines 128–177).</li>
    <li><strong><code>Ticket::selectRoute()</code></strong>: Validates route choice within a <code>while</code> loop, using <code>try-catch</code> and <code>cin.clear()</code> for invalid inputs (Lines 211–242).</li>
    <li><strong><code>BusTicket::setTime()</code>, <code>TrainTicket::setTime()</code>, <code>FlightTicket::setTime()</code></strong>: Use <code>while</code> loops with <code>try-catch</code> to validate time choices, requiring valid input without default fallbacks (Lines 269–294, 304–326, 351–372).</li>
    <li><strong><code>buyTicket()</code></strong>: Validates ticket type choice within a <code>while</code> loop using <code>try-catch</code> and <code>cin.clear()</code> (Lines 512–548).</li>
    <li><strong>Purpose</strong>: Ensures robust input validation by looping until valid input is provided, handling errors gracefully without recursion or default fallbacks.</li>
</ul>

<h3>4.7 File Handling</h3>
<p><strong>Definition</strong>: Reads/writes data to files for persistent storage.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Writing</strong>: <code>FileSystem::storeTicketInformation</code> (Lines 372–388) appends ticket/passenger data to <code>buyer_details.txt</code>.</li>
    <li><strong>Reading</strong>: <code>displayBuyerDetailsByTicketID</code> (Lines 390–426) retrieves and displays ticket details.</li>
    <li><strong>Purpose</strong>: Persists booking data and enables retrieval by ticket ID.</li>
</ul>

<h3>4.8 Constructor & Destructor</h3>
<p><strong>Definition</strong>: Constructors initialize objects; destructors clean up resources.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Constructors</strong>: <code>Passenger</code> (Line 128), <code>Ticket</code> (Line 193), <code>BusTicket</code>/<code>TrainTicket</code>/<code>FlightTicket</code> (Lines 265, 300, 347), <code>Payment</code> (Line 410), <code>PassengerPayment</code> (Line 441).</li>
    <li><strong>Destructor</strong>: Virtual destructor in <code>Payment</code> (Line 415) for proper cleanup.</li>
    <li><strong>Purpose</strong>: Initializes objects and ensures safe resource deallocation.</li>
</ul>

<h3>4.9 Static Members</h3>
<p><strong>Definition</strong>: Shared class-level members across all instances.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>Ticket::totalTicketsSold</code></strong>: Tracks ticket sales (Lines 84, 189, 255, 263).</li>
    <li><strong>Purpose</strong>: Manages first-customer discount and ticket count.</li>
</ul>

<h3>4.10 Friend Function/Class</h3>
<p><strong>Definition</strong>: Grants access to private/protected members.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>FileSystem</code></strong>: Friend of <code>Passenger</code> (Line 74) and <code>Ticket</code> (Line 108) to access private/protected data in <code>storeTicketInformation</code> (Lines 372–388).</li>
    <li><strong>Purpose</strong>: Enables file storage without exposing data via getters.</li>
</ul>

<h3>4.11 Generic Class/Function</h3>
<p><strong>Definition</strong>: Uses templates for reusable code across data types.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>roundUp</code></strong>: Template function rounds numbers to a multiple (Lines 52–56, 122–125, used in Lines 250, 257).</li>
    <li><strong>Purpose</strong>: Ensures consistent fare rounding.</li>
</ul>

<h2>5. Summary Table</h2>
<table>
    <tr>
        <th>Topic</th>
        <th>Key Locations (Lines)</th>
        <th>Key Classes/Functions</th>
    </tr>
    <tr>
        <td>Encapsulation</td>
        <td>59–79, 81–110, 128–177, 193–263</td>
        <td><code>Passenger</code>, <code>Ticket</code></td>
    </tr>
    <tr>
        <td>Inheritance</td>
        <td>81–110, 112–131, 512–548</td>
        <td><code>Ticket</code>, <code>BusTicket</code>, <code>TrainTicket</code>, <code>FlightTicket</code></td>
    </tr>
    <tr>
        <td colspan="3"><strong>Summary of OOP Implementation</strong></td>
    </tr>
    <tr>
        <td>Polymorphism (Compile-Time)</td>
        <td>195–202, 204</td>
        <td><code>applyDiscount</code></td>
    </tr>
    <tr>
        <td>Polymorphism (Run-Time)</td>
        <td>104–105, 267, 302, 349, 269–294, 304–326, 351–372, 512–548</td>
        <td><code>calculateFare</code>, <code>setTime</code></td>
    </tr>
    <tr>
        <td>Abstraction</td>
        <td>81–110, 104–105, 267, 302, 349, 269–294, 304–326, 351–372</td>
        <td><code>Ticket</code></td>
    </tr>
    <tr>
        <td>Diamond Problem</td>
        <td>407–458, 418, 425, 432, 454</td>
        <td><code>Payment</code>, <code>Bkash</code>, <code>Nagad</code>, <code>Bank</code>, <code>PassengerPayment</code></td>
    </tr>
    <tr>
        <td>Error Handling</td>
        <td>128–177, 211–242, 269–294, 304–326, 351–372, 512–548</td>
        <td><code>setInfo</code>, <code>selectRoute</code>, <code>setTime</code>, <code>buyTicket</code></td>
    </tr>
    <tr>
        <td>File Handling</td>
        <td>372–426</td>
        <td><code>storeTicketInformation</code>, <code>displayBuyerDetailsByTicketID</code></td>
    </tr>
    <tr>
        <td>Constructor & Destructor</td>
        <td>128, 193, 265, 300, 347, 410, 419, 426, 433, 441, 415</td>
        <td><code>Passenger</code>, <code>Ticket</code>, <code>Payment</code>, derived classes</td>
    </tr>
    <tr>
        <td>Static Members</td>
        <td>84, 189, 255, 263</td>
        <td><code>Ticket::totalTicketsSold</code></td>
    </tr>
    <tr>
        <td>Friend Function/Class</td>
        <td>74, 108, 372–388</td>
        <td><code>FileSystem</code></td>
    </tr>
    <tr>
        <td>Generic Class/Function</td>
        <td>52–56, 122–125, 250, 257</td>
        <td><code>roundUp</code></td>
    </tr>
</table>

<h2>6. Conclusion</h2>
<p>The <strong>Ticket Reservation System</strong> effectively implements all specified OOP concepts, including encapsulation, inheritance, polymorphism, abstraction, and virtual inheritance to resolve the diamond problem. It ensures robustness with enhanced error handling using while loops, persists data via file handling, and enhances functionality with static members and generic functions. The modular, reusable design makes it a strong example of OOP principles in action.</p>
