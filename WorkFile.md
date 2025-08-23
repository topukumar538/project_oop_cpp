# project_oop_cpp  
Doctor Recommendation System is the official project title. **Doctor Hunter** is our team’s chosen name for the system.

# Doctor Hunter  
This project suggests suitable doctors based on patient complaints using structured symptom-to-doctor mapping.

# 🛠️ Work Instructions

Each team member must create their own `.md` file in this workfile folder.  
In your Markdown file, please share:

- Your ideas and contributions to the project
- Code snippets you've written or improved
- Explanations of your logic and design choices
- Any challenges or suggestions for the team

This will help us stay organized and showcase everyone's work clearly.

---

# 📄 Sample Code

# csv version
`````cpp
#include <iostream>
#include <vector>
#include <fstream>
#include <sstream>
#include <algorithm>


using namespace std;

//abstract
class Recommendation {
public:
    virtual void display() = 0;
};

class Person : public Recommendation {
protected:
    string name;
    string contact;
public:
    Person (string n = "", string c = "") {
        name = n; contact = c;
    }

    void display() {
        cout << "Name: " << name << "\nContact: " << contact << endl;
    }

    string getName() { return name; }
    string getContact() { return contact; }
};

//Doctor class

class Doctor : public Person {
    string problem;
    string department;
    string location;
    double rating;

    friend void updateRating( Doctor &d, double newRating, string newLoc );

public:
    Doctor(string prob = "", string n = "", string dept = "", string c = "", double r = 0.0, string loc = "")
        : Person(n, c) {
            problem = prob; department = dept; location = loc; rating = r;
        }

        void display() {
            Person :: display();
            cout << "Problem: " << problem 
            << "\nDepartment: " << department
            << "\nLocation: " << location
            << "\nRating: " << rating << endl;
        }

        string getSpecialty() { return problem; }
        string getDepartment() { return department; }
        string getLocation() { return location; }
        double getRating() { return rating; }
};

void updateRating (Doctor &d, double newRating, string newLoc) { // friend function
    while (true) {
        if (newRating > 0 && newRating < 5 ){
            d.rating = newRating;
            break;
        }
        cout << "\n enter rating 0 to 5 : ";
        cin >> newRating;
    }
}


// Generic Template Functions
template <typename T>
void showAll(vector<T> &items) {
    for (auto &item : items) {
        item.display();
        cout << "\n----------------------\n";
    }
}

template <class T>
void addItem(vector<T> &items, const T &item) {
    items.push_back(item);
}

template <class T>
T* findByName (vector<T> &items, string &name) {
    for (auto &item : items) {
        if (item.getName() == name) return &item;
    }
    return nullptr;
} 

// =====================
// Data Loader Class
// =====================

class DataLoader {
public:
    void loadDoctors (string file, vector<Doctor> &doctors) {
        ifstream fin(file);
        if (!fin) {
            throw runtime_error("File not Found");
        }

        string line;
        getline(fin, line); // skip header row

        while (getline(fin, line)) {
            if (line.empty()) continue;
            stringstream ss(line);
            string prob, n, dept, c, rstr, loc;
            getline(ss, prob, ',');
            getline(ss, n, ',');
            getline(ss, dept, ',');
            getline(ss, c, ',');
            getline(ss, rstr, ',');
            getline(ss, loc, ',');

            
            double r = stod(rstr);
            addItem(doctors, Doctor(prob, n, dept, c, r, loc));

        }
    }

    
};


void saveDoctors( string &file, vector<Doctor> &doctors) {
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
    vector<Doctor>  &doctors;
    string csvFile;
public:
    Admin(vector<Doctor> &d, string file) 
        : doctors(d), csvFile(file) {}

    void display() {}
    
    void admin1() {
        while (true) {
            cout << "\n[Admin Menu] show | add | save | back> " <<endl;

            string cmd;
            getline(cin, cmd);
            transform(cmd.begin(), cmd.end(), cmd.begin(), ::tolower);

            if (cmd == "show") {
                showAll(doctors);
            } else if (cmd == "add") {
                string prob, n, dept, c, loc; double r;
                cout << "problem: " ;
                getline(cin, prob);
                cout << "Name: ";
                getline(cin, n);
                cout << "Department: ";
                getline(cin, dept);
                cout <<"Contract: ";
                getline(cin, c);
                cout << "Rating: ";
                cin >> r;
                cin.ignore();
                cout << "Location: ";
                getline(cin, loc);

                addItem(doctors, Doctor(prob, n, dept, c, r, loc));

            } else if (cmd == "save") {
                saveDoctors(csvFile, doctors);
            } else if (cmd == "back" ) {
                break;
            } else {
                cout << "Unknown command.\n";
            }


        }
    }
};


class User : public Recommendation {
    vector<Doctor> &doctors;
public:
    User(vector<Doctor> &d) : doctors(d) {}

    void display() {}

    void user1() { //////////////////////////////////
        cout << "Enter problem/specialty: ";
        string s; getline(cin, s);
        bool found = false;

        for (auto &d : doctors) {
            if (d.getSpecialty() == s) {
                d.display();
                cout << "----------------------\n";
                found = true;
            }
        }
        if (!found) {
            cout << "No doctors found for that specialty.\n";
        }
    }

};



int main() {
    vector<Doctor> doctors;
    string CSV = "doctors.csv";
    try {
        DataLoader d;
        d.loadDoctors (CSV, doctors);
    } catch (...) {
        cout << "Not loaded\n";
    }

    cout << "Mode [user/admin]: ";
    string mode;
    getline(cin, mode);
    transform(mode.begin(), mode.end(), mode.begin(), ::tolower);

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
    else if (mode =="user") {
        User user(doctors);
        user.user1();
    }
    else {
        cout << "Invalid mode.\n";
    }
    cout << "Goodbye!\n";

}

`````

````csv

Problem,Name,Department,Contact,Rating,Location
chest pain,Dr. Ayesha Rahman,Cardiology,017XXXXXXXX,4.8,Dhaka
chest pain,Dr. Tanvir Islam,Pulmonology,018XXXXXXXX,4.6,Rajshahi
skin rash,Dr. Farhana Kabir,Dermatology,019XXXXXXXX,4.9,Chattogram
skin rash,Dr. Imran Hossain,Dermatology,017YYYYYYYY,4.7,Sylhet
headache,Dr. Mahmud Hasan,Neurology,016XXXXXXXX,4.7,Sylhet
fever,Dr. Nusrat Jahan,General Medicine,015XXXXXXXX,4.5,Khulna

`````

# doctors.csv file then add the data then run the programme

# 📚 Learning Goals  
We need to understand each header file and data structure used in this code. Once we grasp how they work, we’ll be able to confidently update and expand the project.

