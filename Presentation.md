## **Lab Name : Object Oriented Programming Sessional**
## **Submission Date : August 27, 2025**
### **Group Members :**
| Name             | Roll        |
|------------------|-------------|
| Topu  | 2310003    |
| Saba | 2310013     |
| Tahsin | 2310021     |

---

<h2>1. Project Title</h2>
<p><strong>Doctor Recommendation System</strong></p>

<h2>2. Project Summary</h2>
<p>A C++-based console application that allows users to search for doctors by their specialty, while admins can view, add, and save doctor details to a CSV file. The system demonstrates object-oriented principles such as inheritance, polymorphism, abstraction, and file handling. It supports both <strong>user mode</strong> (doctor search by problem) and <strong>admin mode</strong> (manage doctor data).</p>



### **Code:**

```C++
/* Save this file as main.cpp and compile with a C++17 (or later) compiler:
   g++ -std=c++17 main.cpp -o doctor_reco
   or on Windows (MinGW): g++ -std=c++17 main.cpp -o doctor_reco.exe
*/
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

<h2>3. Program Functionality</h2>
<p>The <strong>Doctor Recommendation System</strong> is a console-based C++ application. Its key functionalities include:</p>
<ul>
    <li>Loading doctor data from a CSV file.</li>
    <li>Allowing users to search doctors by problem/specialty (case-insensitive).</li>
    <li>Allowing admins (with a pin) to show all doctors, add doctors, and save the data back to the CSV file.</li>
    <li>Using OOP principles: inheritance, polymorphism (via virtual functions), abstraction, encapsulation, friend functions, templates, and virtual inheritance to address the diamond problem.</li>
</ul>

<h2>4. Implementation of OOP Concepts</h2>

<h3>4.1 Encapsulation</h3>
<p><strong>Definition</strong>: Bundles data and methods into classes, restricting access using private/protected specifiers and exposing functionality via public methods.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>Person</code> Class</strong>: Protected data (<code>name</code>, <code>contact</code>) with public methods (<code>display()</code>, <code>getName()</code>, <code>getContact()</code>). Location: Lines ~20–45.</li>
    <li><strong><code>Doctor</code> Class</strong>: Private data (<code>problem</code>, <code>department</code>, <code>location</code>, <code>rating</code>) with public methods (<code>display()</code>, <code>getLocation()</code>, <code>getRating()</code>). Location: Lines ~70–115.</li>
</ul>

<h3>4.2 Inheritance</h3>
<p><strong>Definition</strong>: Derived classes inherit properties/methods from a base class for code reuse.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Base Class</strong>: <code>Person</code> (Lines ~20–45).</li>
    <li><strong>Intermediate Classes</strong>: <code>Contactable</code>, <code>Identifiable</code> (Lines ~48–60).</li>
    <li><strong>Derived Class</strong>: <code>Doctor</code> inherits from <code>Contactable</code> and <code>Identifiable</code> (Lines ~70–115).</li>
</ul>

<h3>4.3 Polymorphism</h3>
<p><strong>Definition</strong>: Enables methods to behave differently based on the object. Includes compile-time (overloading) and run-time (virtual functions).</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Run-Time (Virtual Functions)</strong>: <code>Recommendation::display()</code> is pure virtual and implemented by derived classes (<code>Person</code>, <code>Doctor</code>, <code>Admin</code>, <code>User</code>). Location: Lines ~10–95.</li>
</ul>

<h3>4.4 Abstraction</h3>
<p><strong>Definition</strong>: Hides implementation details, exposing only essential functionality via abstract classes.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Abstract Class</strong>: <code>Recommendation</code> with pure virtual function <code>display()</code> (Lines ~10–15).</li>
</ul>

<h3>4.5 Diamond Problem</h3>
<p><strong>Definition</strong>: Occurs in multiple inheritance with a shared base class, resolved using virtual inheritance.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Setup</strong>: <code>Contactable</code> and <code>Identifiable</code> are both virtually derived from <code>Person</code> (Lines ~48–60), and <code>Doctor</code> inherits from both (Lines ~70–75).</li>
</ul>

<h3>4.6 Error Handling</h3>
<p><strong>Definition</strong>: Uses try–catch blocks to manage exceptions robustly.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>DataLoader::loadDoctors()</code></strong>: Throws <code>runtime_error("File Not Found")</code> if CSV missing (Lines ~150–170).</li>
    <li><strong><code>main()</code></strong>: Wraps loading in a try–catch and prints a fallback message (Lines ~300–325).</li>
</ul>

<h3>4.7 File Handling (CSV)</h3>
<p><strong>Definition</strong>: Reads/writes data to CSV for persistent storage.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Reading</strong>: <code>DataLoader::loadDoctors()</code> parses CSV using <code>ifstream</code> and <code>stringstream</code> (Lines ~150–180).</li>
    <li><strong>Writing</strong>: <code>saveDoctors()</code> writes header and rows to CSV (Lines ~190–210).</li>
</ul>

<h3>4.8 Constructor & Destructor</h3>
<p><strong>Definition</strong>: Constructors initialize objects; destructors clean up resources.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Constructors</strong>: <code>Person</code>, <code>Contactable</code>, <code>Identifiable</code>, <code>Doctor</code> provide constructors to initialize members (Lines ~20–90).</li>
</ul>

<h3>4.9 Friend Function</h3>
<p><strong>Definition</strong>: Grants access to private/protected members.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Friend functions</strong>: <code>getSpecialty()</code>, <code>getDepartment()</code> are friends of <code>Doctor</code> (Lines ~75–95).</li>
</ul>

<h3>4.10 Generic Function/Template</h3>
<p><strong>Definition</strong>: Template functions provide reusable behavior for different types.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Templates</strong>: <code>showAll()</code> and <code>addItem()</code> are template functions used to operate on vectors of objects (Lines ~120–140).</li>
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
        <td>20–45, 70–115</td>
        <td><code>Person</code>, <code>Doctor</code></td>
    </tr>
    <tr>
        <td>Inheritance</td>
        <td>48–75</td>
        <td><code>Contactable</code>, <code>Identifiable</code>, <code>Doctor</code></td>
    </tr>
    <tr>
        <td>Polymorphism (Run-Time)</td>
        <td>10–95</td>
        <td><code>Recommendation::display()</code>, <code>Person::display()</code>, <code>Doctor::display()</code></td>
    </tr>
    <tr>
        <td>Abstraction</td>
        <td>10–15</td>
        <td><code>Recommendation</code></td>
    </tr>
    <tr>
        <td>Diamond Problem</td>
        <td>48–75</td>
        <td><code>Contactable</code>, <code>Identifiable</code>, <code>Doctor</code></td>
    </tr>
    <tr>
        <td>Error Handling</td>
        <td>150–180, 300–325</td>
        <td><code>DataLoader::loadDoctors()</code>, <code>main()</code></td>
    </tr>
    <tr>
        <td>File Handling</td>
        <td>150–210</td>
        <td><code>loadDoctors()</code>, <code>saveDoctors()</code></td>
    </tr>
    <tr>
        <td>Constructor & Destructor</td>
        <td>20–90</td>
        <td>Constructors of classes</td>
    </tr>
    <tr>
        <td>Friend Function</td>
        <td>75–95</td>
        <td><code>getSpecialty()</code>, <code>getDepartment()</code></td>
    </tr>
    <tr>
        <td>Generic Function</td>
        <td>120–140</td>
        <td><code>showAll()</code>, <code>addItem()</code></td>
    </tr>
</table>

<h2>6. Conclusion</h2>
<p>The <strong>Doctor Recommendation System</strong> demonstrates core OOP concepts clearly and provides a simple, extensible console application for managing and searching doctor records. To run the project:</p>
<ol>
    <li>Create a file named <code>main.cpp</code> and paste the C++ code above.</li>
    <li>Create a CSV file (example below) and update the <code>CSV</code> filepath in <code>main.cpp</code>.</li>
</ol>
<p><strong>Example CSV (info.csv)</strong>:</p>
<pre>
Problem,Name,Department,Contact,Rating,Location
Cardiology,Dr. A,Cardiology,01234567890,4.5,Dhaka Medical Center
Diabetes,Dr. B,Endocrinology,01987654321,4.2,City Clinic
</pre>
<p>If you'd like, I can also create the <code>main.cpp</code> file and a sample <code>info.csv</code> and bundle them into a downloadable ZIP — tell me "create files".</p>
