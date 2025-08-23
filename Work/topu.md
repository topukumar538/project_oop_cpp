````cpp
#include <iostream>
#include <vector>
#include <fstream>
#include <sstream>
#include <algorithm>
#include <iomanip> // for setw

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

    friend void updateRating(Doctor &d, double newRating, string newLoc);

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

    string getSpecialty() const { return problem; }
    string getDepartment() const { return department; }
    string getLocation() const { return location; }
    double getRating() const { return rating; }
};

void updateRating(Doctor &d, double newRating, string newLoc) {
    while (true) {
        if (newRating >= 0 && newRating <= 5) {
            d.rating = newRating;
            d.location = newLoc;
            break;
        }
        cout << "\nEnter rating 0 to 5: ";
        cin >> newRating;
    }
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

template <class T>
T* findByName(vector<T> &items, string &name) {
    for (auto &item : items) {
        if (item.getName() == name) return &item;
    }
    return nullptr;
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
            getline(ss, loc); // FIXED: read till end

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
        fout << d.getSpecialty() << ","
             << d.getName() << ","
             << d.getDepartment() << ","
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
            cout << "\n[Admin Menu] show | add | save | back\n> ";
            string cmd;
            getline(cin, cmd);
            cmd = toLower(cmd);

            if (cmd == "show") {
                showAll(doctors);
            } else if (cmd == "add") {
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

            } else if (cmd == "save") {
                saveDoctors(csvFile, doctors);
            } else if (cmd == "back") {
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
            if (toLower(d.getSpecialty()) == s) {
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
    string CSV = "doctors.csv";
    try {
        DataLoader d;
        d.loadDoctors(CSV, doctors);
    } catch (...) {
        cout << "Doctors data not loaded.\n";
    }

    cout << "Mode [user/admin]: ";
    string mode;
    getline(cin, mode);
    mode = toLower(mode);

    if (mode == "admin") {
        cout << "Enter admin password: ";
        string password;
        getline(cin, password);
        if (password != "123") {
            cout << "Incorrect password. Exiting.\n";
            return 0;
        }
        Admin admin(doctors, CSV);
        admin.admin1();
    }
    else if (mode == "user") {
        User user(doctors);
        user.user1();
    }
    else {
        cout << "Invalid mode.\n";
    }
    cout << "Goodbye!\n";
}
````
