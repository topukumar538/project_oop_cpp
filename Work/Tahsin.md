```C
#include <iostream>
#include <vector>
#include <fstream>
#include <sstream>
#include <algorithm> // for tolower
#include <iomanip>   // for decimal point
using namespace std;

// Abstract base
class Recommendation {
public:
    virtual void display() = 0;
};

// =====================
// Person Class
// =====================
class Person : public Recommendation {
protected:
    string name;
    string contact;
public:
    Person(string n = "", string c = "") : name(n), contact(c) {}

    void display() override {
        cout << "Name     : " << name << "\n"
             << "Contact  : " << contact << endl;
    }

    string getName() const { return name; }
    string getContact() const { return contact; }
};


// =====================
// Doctor Class
// =====================
class Doctor : public Person {
    string problem;
    string department;
    string location;
    double rating;

    
    friend string getSpecialty(const Doctor &d);
    friend string getDepartment(const Doctor &d);

public:
    Doctor(string prob = "", string n = "", string dept = "", string c = "", double r = 0.0, string loc = "")
        : Person(n, c), problem(prob), department(dept), location(loc), rating(r) {}

    void display() override {
        cout << "=============================================\n";
        cout << "Doctor Information\n";
        cout << "---------------------------------------------\n";
        Person::display();
        cout << "Problem  : " << problem << "\n"
             << "Dept     : " << department << "\n"
             << "Location : " << location << "\n"
             << "Rating   : " << fixed << setprecision(1) << rating << " / 5.0\n";
        cout << "=============================================\n";
    }

    string getLocation() const { return location; }
    double getRating() const { return rating; }
};

// =====================
// Friend function definitions
// =====================
string getSpecialty(const Doctor &d) {
    return d.problem;
}

string getDepartment(const Doctor &d) {
    return d.department;
}

// =====================
// Generic Template Functions
// =====================
template <typename T>
void showAll(vector<T> &items) {
    for (auto &item : items) {
        item.display();
    }
}

template <class T>
void addItem(vector<T> &items, const T &item) {
    items.push_back(item);
}

// Helper: lowercase conversion
string toLower(string s) {
    transform(s.begin(), s.end(), s.begin(), ::tolower);
    return s;
}

// =====================
// Data Loader Class
// =====================
class DataLoader {
public:
    void loadDoctors(string file, vector<Doctor> &doctors) {
        ifstream fin(file);
        if (!fin) throw runtime_error("File not Found");

        string line;
        getline(fin, line); // skip header

        while (getline(fin, line)) {
            if (line.empty()) continue;
            stringstream ss(line);
            string prob, n, dept, c, rstr, loc;
            getline(ss, prob, ',');
            getline(ss, n, ',');
            getline(ss, dept, ',');
            getline(ss, c, ',');
            getline(ss, rstr, ',');
            getline(ss, loc);

            double r = stod(rstr);
            addItem(doctors, Doctor(prob, n, dept, c, r, loc));
        }
    }
};

// =====================
// Save Doctors
// =====================
void saveDoctors(const string &file, vector<Doctor> &doctors) {
    ofstream fout(file);
    fout << "Problem,Name,Department,Contact,Rating,Location\n";
    for (auto &d : doctors) {
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
    vector<Doctor> &doctors;
    string csvFile;
public:
    Admin(vector<Doctor> &d, string file) : doctors(d), csvFile(file) {}

    void display() override {}

    void admin1() {
        while (true) {
            cout << "\n[Admin Menu] 1.show | 2.add | 3.save | 4.exit\n> ";
            int cmd;
            cin >> cmd;
            cin.ignore();

            if (cmd == 1) {
                showAll(doctors);
            } else if (cmd == 2) {
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
                saveDoctors(csvFile, doctors);
            } else if (cmd == 4) {
                break;
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
    vector<Doctor> &doctors;
public:
    User(vector<Doctor> &d) : doctors(d) {}

    void display() override {}

    void user1() {
        cout << "Enter problem/specialty: ";
        string s; getline(cin, s);
        s = toLower(s);
        bool found = false;

        for (auto &d : doctors) {
            if (toLower(getSpecialty(d)) == s) {
                d.display();
                found = true;
            }
        }
        if (!found) {
            cout << "No doctors found for that specialty.\n";
        }
    }
};

// =====================
// Main
// =====================
int main() {
    vector<Doctor> doctors;
    string CSV = "D:/Tahsin/Documents/1st year even/Project/info.csv";
    try {
        DataLoader d;
        d.loadDoctors(CSV, doctors);
    } catch (...) {
        cout << "Doctors data not loaded.\n";
    }

    cout << "Mode [1.user/2.admin]: ";
    int mode;
    cin >> mode;
    cin.ignore();

    if (mode == 2) {
        cout << "Enter admin pin: ";
        int pin;
        cin >> pin;
        if (pin != 123) {
            cout << "Incorrect pin. Exiting.\n";
            return 0;
        }
        Admin admin(doctors, CSV);
        admin.admin1();
    }
    else if (mode == 1) {
        User user(doctors);
        user.user1();
    }
    else {
        cout << "Invalid mode.\n";
    }
    cout << "Goodbye! THANK YOU for using our system....\n";
}

```
