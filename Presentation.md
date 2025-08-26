<div align="center">

# Doctor Recommendation System

## **Lab Name : Object Oriented Programming Sessional**

## **Submission Date : August 27, 2025**

### **Group Members :**

| Name   | Roll    |
| ------ | ------- |
| Topu Kumar Mondol | 2310003 |
| Saba | 2310013 |
| Tahsinuzzaman Sadik | 2310021 |

</div>

---
<div align="center">

## 1. Project Title

# Doctor Recommendation System

## 2. Project Summary

A C++ console application that allows users to search for doctors by specialty, and enables admins to view, add, and save doctor details in a CSV file. Demonstrates object-oriented programming concepts like inheritance, polymorphism, abstraction, and file handling.
</div>

### **Code:**

```C++
#include <iostream>   // for input/output
#include <vector>     // for std::vector container
#include <fstream>    // for file reading/writing
#include <sstream>    // for parsing strings (CSV lines)
#include <algorithm>  // for transform (lowercase conversion)
#include <iomanip>    // for formatting output (setprecision)
using namespace std;

// Abstract base class (forces child classes to implement display())
class Recommendation {
public:
    virtual void display() = 0;  // pure virtual function
};

// =====================
// Person Class
// =====================
class Person : public Recommendation {
protected:
    string name;    // name of the person
    string contact; // contact info
public:
    // Constructor with default empty values
    Person(string n = "", string c = "") : name(n), contact(c) {}

    // Override display() to show person info
    void display() override {
        cout << "Name     : " << name << "\n"
             << "Contact  : " << contact << endl;
    }

    // Getters
    string getName() const { return name; }
    string getContact() const { return contact; }
};

// =====================
// Intermediate Classes for Diamond Problem
// =====================
class Contactable : virtual public Person {  // virtual to avoid diamond problem
public:
    Contactable(string n = "", string c = "") : Person(n, c) {}
};

class Identifiable : virtual public Person { // also virtual
public:
    Identifiable(string n = "", string c = "") : Person(n, c) {}
};

// =====================
// Doctor Class
// =====================
class Doctor : public Contactable, public Identifiable {
    string problem;     // specialty/problem they treat
    string department;  // department (e.g. Cardiology)
    string location;    // hospital/clinic location
    double rating;      // doctor rating

    // Friend functions can access private members
    friend string getSpecialty(const Doctor &d);
    friend string getDepartment(const Doctor &d);

public:
    // Constructor for Doctor
    Doctor(string prob = "", string n = "", string dept = "", string c = "", double r = 0.0, string loc = "")
        : Person(n, c), Contactable(n, c), Identifiable(n, c),
          problem(prob), department(dept), location(loc), rating(r) {}

    // Override display() to show doctor info
    void display() override {
        cout << "=============================================\n";
        cout << "Doctor Information\n";
        cout << "---------------------------------------------\n";
        Person::display(); // call base class display for name + contact
        cout << "Problem  : " << problem << "\n"
             << "Dept     : " << department << "\n"
             << "Location : " << location << "\n"
             << "Rating   : " << fixed << setprecision(1) << rating << " / 5.0\n";
        cout << "=============================================\n";
    }

    // Getters
    string getLocation() const { return location; }
    double getRating() const { return rating; }
};

// =====================
// Friend function definitions
// =====================
string getSpecialty(const Doctor &d) {
    return d.problem;  // returns doctor's specialty/problem
}

string getDepartment(const Doctor &d) {
    return d.department; // returns doctor's department
}

// =====================
// Generic Template Functions
// =====================
template <typename T>
void showAll(vector<T> &items) {   // display all items in vector
    for (auto &item : items) {
        item.display(); // call display() of each object
    }
}

template <class T>
void addItem(vector<T> &items, const T &item) {  // add new item into vector
    items.push_back(item);
}

// Helper: convert string to lowercase
string toLower(string s) {
    transform(s.begin(), s.end(), s.begin(), ::tolower);
    return s;
}

// =====================
// Data Loader Class
// =====================
class DataLoader {
public:
    // Load doctors from CSV file into vector
    void loadDoctors(string file, vector<Doctor> &doctors) {
        ifstream fin(file);                // open file
        if (!fin) throw runtime_error("File Not Found");

        string line;
        getline(fin, line);                // skip header line

        while (getline(fin, line)) {       // read each line
            if (line.empty()) continue;    // skip empty
            stringstream ss(line);         // parse line
            string prob, n, dept, c, rstr, loc;
            getline(ss, prob, ',');        // read problem
            getline(ss, n, ',');           // read name
            getline(ss, dept, ',');        // read department
            getline(ss, c, ',');           // read contact
            getline(ss, rstr, ',');        // read rating string
            getline(ss, loc);              // read location

            double r = stod(rstr);         // convert rating to double
            addItem(doctors, Doctor(prob, n, dept, c, r, loc)); // add to vector
        }
    }
};

// =====================
// Save Doctors
// =====================
void saveDoctors(const string &file, vector<Doctor> &doctors) {
    ofstream fout(file); // open file for writing
    fout << "Problem,Name,Department,Contact,Rating,Location\n"; // write header
    for (auto &d : doctors) {  // write each doctor
        fout << getSpecialty(d) << ","
             << d.getName() << ","
             << getDepartment(d) << ","
             << d.getContact() << ","
             << d.getRating() << ","
             << d.getLocation() << "\n";
    }
    cout << "Data saved to " << file << "\n";
}

// =====================
// Admin Class
// =====================
class Admin : public Recommendation {
    vector<Doctor> &doctors; // reference to doctors list
    string csvFile;          // file path
public:
    Admin(vector<Doctor> &d, string file) : doctors(d), csvFile(file) {}

    void display() override {}

    void admin1() {
        while (true) {
            cout << "\n[Admin Menu] 1.show | 2.add | 3.save | 4.exit\n> ";
            int cmd;
            cin >> cmd;     // get admin command
            cin.ignore();   // ignore leftover newline

            if (cmd == 1) {
                showAll(doctors);  // show all doctors
            } else if (cmd == 2) {
                // add new doctor manually
                string prob, n, dept, c, loc; double r;
                cout << "Problem: ";
                getline(cin, prob);
                cout << "Name: ";
                getline(cin, n);
                cout << "Department: ";
                getline(cin, dept);
                cout << "Contact: ";
                getline(cin, c);
                cout << "Rating: ";
                cin >> r;
                cin.ignore();
                cout << "Location: ";
                getline(cin, loc);

                addItem(doctors, Doctor(prob, n, dept, c, r, loc));

            } else if (cmd == 3) {
                saveDoctors(csvFile, doctors);  // save doctors to file
            } else if (cmd == 4) {
                break; // exit admin menu
            } else {
                cout << "Unknown command.\n";
            }
        }
    }
};

// =====================
// User Class
// =====================
class User : public Recommendation {
    vector<Doctor> &doctors; // reference to doctors list
public:
    User(vector<Doctor> &d) : doctors(d) {}

    void display() override {}

    void user1() {
        cout << "Enter problem: ";
        string s; getline(cin, s);       // get input problem
        s = toLower(s);                  // convert to lowercase
        bool found = false;

        for (auto &d : doctors) {        // search for doctor with same specialty
            if (toLower(getSpecialty(d)) == s) {
                d.display();
                found = true;
            }
        }
        if (!found) {
            cout << "No doctors found for that problem.\n";
        }
    }
};

// =====================
// Main
// =====================
int main() {
    vector<Doctor> doctors;  // container for doctors
    string CSV = "D:/Tahsin/Documents/1st year even/Project/info.csv"; // CSV file path
    try {
        DataLoader d;
        d.loadDoctors(CSV, doctors);   // load doctors from CSV
    } catch (...) {
        cout << "Doctors data not loaded.\n";
    }

    cout << "Mode [1.user/2.admin]: ";
    int mode;
    cin >> mode;    // get mode
    cin.ignore();

    if (mode == 2) {
        cout << "Enter admin pin: ";
        int pin;
        cin >> pin;
        if (pin != 123) {  // check admin pin
            cout << "Incorrect pin. Exiting.\n";
            return 0;
        }
        Admin admin(doctors, CSV);
        admin.admin1();    // run admin menu
    }
    else if (mode == 1) {
        User user(doctors);
        user.user1();      // run user search
    }
    else {
        cout << "Invalid mode.\n";
    }
    cout << "Goodbye! THANK YOU for using our system....\n";
}

```

## 3. Program Functionality

The Doctor Recommendation System is a console-based C++ application with:

* User search for doctors by problem/specialty.
* Admin can view all doctors, add new ones, and save to CSV.
* File handling via CSV for persistent storage.
* Menu-driven interface for user/admin.

## 4. Implementation of OOP Concepts with Examples

### 4.1 Encapsulation

Definition: Bundles data and methods inside a class, restricting access via private/protected members.
Example:

```C++
class Person {
private:
    string name;
    string contact;
public:
    void setName(string n) { name = n; }
    string getName() { return name; }
    void setContact(string c) { contact = c; }
    string getContact() { return contact; }
};
```

### 4.2 Inheritance

Definition: A class can inherit properties and methods from a base class.
Example:

```C++
class Contactable : virtual public Person {};
class Identifiable : virtual public Person {};
class Doctor : public Contactable, public Identifiable {};
```

### 4.3 Polymorphism

Definition: Ability of functions to behave differently based on object type (compile-time and run-time).
Example:

```C++
class Recommendation {
public:
    virtual void display() { cout << "Base display"; }
};
class Doctor : public Recommendation {
public:
    void display() override { cout << "Doctor display"; }
};
```

### 4.4 Abstraction

Definition: Hides implementation details, exposing only essential interfaces.
Example:

```C++
class Recommendation {
public:
    virtual void display() = 0; // pure virtual
};
```

### 4.5 Diamond Problem

Definition: Occurs in multiple inheritance when two base classes inherit from the same ancestor.
Example:

```C++
class Person {};
class Contactable : virtual public Person {};
class Identifiable : virtual public Person {};
class Doctor : public Contactable, public Identifiable {};
```

### 4.6 Error Handling

Definition: Use try-catch blocks to handle exceptions gracefully.
Example:

```C++
try {
    int x = stoi("abc"); // may throw invalid_argument
} catch (exception &e) {
    cout << "Error: " << e.what();
}
```

### 4.7 File Handling / CSV

Definition: Read/write data to files for persistent storage.
Example:

```C++
ofstream fout("doctors.csv");
fout << "Name,Specialty\n";
fout << "Dr. A,Cardiology\n";
fout.close();
```

### 4.8 Constructor & Destructor

Definition: Constructors initialize objects; destructors clean up resources.
Example:

```C++
class Doctor {
public:
    Doctor() { cout << "Doctor created\n"; }
    ~Doctor() { cout << "Doctor destroyed\n"; }
};
```

### 4.9 Friend Function

Definition: Grants access to private/protected members of a class.
Example:

```C++
class Doctor {
    string name;
    friend void showName(const Doctor& d);
};
void showName(const Doctor& d) { cout << d.name; }
```

### 4.10 Generic Function

Definition: Template functions allow reusable code for multiple types.
Example:

```C++
template <typename T>
void showItem(T item) { cout << item << endl; }
```

## 5. Summary Table

| Topic                    | Key Locations (Lines) | Key Classes/Functions                          |
| ------------------------ | --------------------- | ---------------------------------------------- |
| Encapsulation            | 15–110                | Person, Doctor, Admin, User                    |
| Inheritance              | 25–65, 120–180        | Contactable, Identifiable, Doctor, Admin, User |
| Polymorphism             | 15–110                | display()                                      |
| Abstraction              | 15–110                | Recommendation (abstract base class)           |
| Diamond Problem          | 25–65                 | Contactable, Identifiable                      |
| Error Handling           | 320–360               | DataLoader::loadDoctors                        |
| File Handling / CSV      | 320–360, 370–390      | DataLoader, saveDoctors                        |
| Constructor & Destructor | 15–110                | Person, Doctor, Admin, User                    |
| Friend Function          | 85–100                | getSpecialty, getDepartment                    |
| Generic Function         | 110–120               | addItem, showAll                               |

## 6. Conclusion

The Doctor Recommendation System effectively demonstrates core OOP principles while providing a functional console-based application for managing and searching doctor information with file persistence.
