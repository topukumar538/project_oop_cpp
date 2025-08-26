````cpp
#include <iostream>
#include <vector>
#include <fstream>
#include <sstream>
#include <iomanip>
#include <algorithm>
using namespace std;

//abstract class
class Recommendation {
public:
    virtual void display() = 0;
};

// person class

class Person : public Recommendation {
protected:
    string name;
    string contact;

public:
    Person(string n = "", string c = "") : name(n), contact(c) {}

    void display() override {
        cout << "Name       : " << name << endl
             << "Contract   : " << contact << endl;
    }

    string getName() { return name; }
    string getContact() { return contact; }
};

// Intermediate Classes for Diamond Problem

class Contactable : virtual public Person {
public:
    Contactable(string n = "", string c = "") : Person(n, c) {}
};

class Identifiable : virtual public Person {
public:
    Identifiable(string n = "", string c = "") : Person(n, c) {}
};

// Doctor Class

class Doctor : public Contactable, public Identifiable {
    string problem;
    string department;
    string location;
    double rating;

    friend string getSpecialty(const Doctor& d);
    friend string getDepartment(const Doctor& d);

public:
    Doctor(string prob = "", string n = "", string dept = "", string c = "", double r = 0.0, string loc = "")
        : Contactable(n, c),
            problem(prob), department(dept), location(loc), rating(r) {}

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

    string getLocation() { return location; }
    double getRating() { return rating; }

};

string getSpecialty(const Doctor &d) {
    return d.problem;
}

string getDepartment(const Doctor &d) {
    return d.department;
}


// Generic Template Functions

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
string toLower( string s) {
    transform(s.begin(), s.end(), s.begin(), ::tolower );
    return s;
}

class DataLoader {
public:
    void loadDoctors(string file, vector<Doctor> &doctors) {
        ifstream fin(file);

        
        if (!fin.is_open()) {
            throw runtime_error("Could not open file: " + file);
        }


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
    cout << "Data saved to " << file << endl;
}


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

// User Class


class User : public Recommendation {
    vector<Doctor> &doctors;
public:
    User(vector<Doctor> &d) : doctors(d) {}
    void display() override {}

    void user1() {
        cout << "Enter problem: ";
        string s;
        getline(cin, s);
        s = toLower(s);
        bool found = false;

        for (auto &d : doctors) {
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



int main() {
    vector<Doctor> doctors;
    string CSV = "doctors.csv";

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
    } else if (mode == 1) {
        User user(doctors);
        user.user1();
    }
    else {
        cout << "Invalid mode.\n";
    }
    cout << "Goodbye! THANK YOU for using our system....\n";
}

````
