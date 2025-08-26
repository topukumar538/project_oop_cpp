```C
#include <iostream>
#include <vector>
#include <fstream>
#include <sstream>
#include <algorithm>
#include <iomanip>
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
// Intermediate Classes for Diamond Problem
// =====================
class Contactable : virtual public Person {
public:
    Contactable(string n = "", string c = "") : Person(n, c) {}
};

class Identifiable : virtual public Person {
public:
    Identifiable(string n = "", string c = "") : Person(n, c) {}
};

// =====================
// Doctor Class
// =====================
class Doctor : public Contactable, public Identifiable {
    string problem;
    string department;
    string location;
    double rating;

    friend string getSpecialty(const Doctor &d);
    friend string getDepartment(const Doctor &d);

public:
    Doctor(string prob = "", string n = "", string dept = "", string c = "", double r = 0.0, string loc = "")
        : Person(n, c), Contactable(n, c), Identifiable(n, c),
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
        if (!fin) throw runtime_error("File Not Found");

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
        cout << "Enter problem: ";
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
            cout << "No doctors found for that problem.\n";
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

```C
Problem,Name,Department,Contact,Rating,Location
chest pain,Dr. Ayesha Rahman,Cardiology,017XXXXXXXX,4.8,Dhaka
chest pain,Dr. Tanvir Islam,Pulmonology,018XXXXXXXX,4.6,Rajshahi
skin rash,Dr. Farhana Kabir,Dermatology,019XXXXXXXX,4.9,Chattogram
skin rash,Dr. Imran Hossain,Dermatology,017YYYYYYYY,4.7,Sylhet
headache,Dr. Mahmud Hasan,Neurology,016XXXXXXXX,4.7,Sylhet
fever,Dr. Nusrat Jahan,General Medicine,015XXXXXXXX,4.5,Khulna
chest pain,Dr. Rezaul Karim,Cardiology,01760123456,4.7,Khulna
chest pain,Dr. Shahnaz Parvin,Pulmonology,01871234567,4.8,Barishal
chest pain,Dr. Mizanur Rahman,Cardiology,01982345678,4.6,Rangpur
skin rash,Dr. Taslima Akter,Dermatology,01693456789,4.9,Dhaka
skin rash,Dr. Jahangir Alam,Dermatology,01704567891,4.7,Khulna
skin rash,Dr. Lubna Sultana,Dermatology,01815678902,4.8,Rajshahi
headache,Dr. Aminul Haque,Neurology,01926789013,4.6,Chattogram
headache,Dr. Farzana Yasmin,Neurology,01537890124,4.9,Barishal
headache,Dr. Kamruzzaman Khan,Neurology,01748901235,4.7,Rangpur
fever,Dr. Sharmin Hossain,General Medicine,01859012346,4.8,Sylhet
fever,Dr. Rashed Mahmud,General Medicine,01960123457,4.6,Chattogram
fever,Dr. Nabila Chowdhury,General Medicine,01671234568,4.7,Dhaka
abdominal pain,Dr. Habibur Rahman,Gastroenterology,01782345679,4.9,Rajshahi
abdominal pain,Dr. Sultana Jahan,Gastroenterology,01893456780,4.8,Khulna
abdominal pain,Dr. Mahfuz Alam,Gastroenterology,01904567891,4.7,Barishal
vomiting,Dr. Rukhsana Akter,General Medicine,01515678902,4.6,Rangpur
vomiting,Dr. Shafiqul Islam,General Medicine,01726789013,4.8,Dhaka
vomiting,Dr. Nasrin Sultana,General Medicine,01837890124,4.7,Sylhet
numbness,Dr. Zahid Hasan,Neurology,01948901235,4.9,Khulna
numbness,Dr. Farhana Rahman,Neurology,01659012346,4.8,Barishal
numbness,Dr. Touhidul Islam,Neurology,01760123457,4.7,Rajshahi
breathlessness,Dr. Lutfun Nahar,Pulmonology,01871234568,4.9,Dhaka
breathlessness,Dr. Anisur Rahman,Pulmonology,01982345679,4.8,Chattogram
breathlessness,Dr. Marufa Akter,Pulmonology,01693456780,4.7,Sylhet
chest pain,Dr. Arman Siddiqui,Cardiology,01760112233,4.8,Dhaka
skin rash,Dr. Morsheda Khatun,Dermatology,01871223344,4.7,Khulna
headache,Dr. Jalal Uddin,Neurology,01982334455,4.9,Barishal
fever,Dr. Tahmina Sultana,General Medicine,01693445566,4.6,Rajshahi
abdominal pain,Dr. Khaled Mahmud,Gastroenterology,01704556677,4.8,Chattogram
vomiting,Dr. Ruma Akhter,General Medicine,01815667788,4.7,Sylhet
numbness,Dr. Shafayat Hossain,Neurology,01926778899,4.9,Rangpur
breathlessness,Dr. Nilufa Yasmin,Pulmonology,01537889900,4.8,Dhaka
high fever,Dr. Ahsan Kabir,Infectious Diseases,01748990011,4.7,Khulna
persistent cough,Dr. Monira Rahman,Pulmonology,01859001122,4.9,Barishal
vision loss,Dr. Rasheda Begum,Ophthalmology,01960112233,4.8,Chattogram
ear pain,Dr. Mahin Chowdhury,ENT,01671223344,4.7,Sylhet
toothache,Dr. Farzana Rahim,Dentistry,01782334455,4.9,Rajshahi
joint pain,Dr. Imtiaz Karim,Rheumatology,01893445566,4.8,Dhaka
back pain,Dr. Nusrat Amin,Orthopedics,01904556677,4.7,Khulna
burn injury,Dr. Shakil Ahmed,Plastic Surgery,01515667788,4.9,Barishal
allergic reaction,Dr. Tamanna Chowdhury,Allergy & Immunology,01726778899,4.8,Chattogram
urinary pain,Dr. Fariha Nahar,Urology,01837889900,4.7,Sylhet
pregnancy care,Dr. Mehedi Hasan,Obstetrics & Gynecology,01948990011,4.9,Rangpur
child fever,Dr. Lamiya Akter,Pediatrics,01659001122,4.8,Dhaka
skin infection,Dr. Rifat Jahan,Dermatology,01760112244,4.7,Khulna
stroke symptoms,Dr. Tanveer Hossain,Neurology,01871223355,4.9,Barishal
diabetes,Dr. Sumaiya Rahman,Endocrinology,01982334466,4.8,Chattogram
thyroid problem,Dr. Omar Faruq,Endocrinology,01693445577,4.7,Sylhet
asthma,Dr. Anika Sultana,Pulmonology,01704556688,4.9,Rajshahi
gastric pain,Dr. Maruf Hasan,Gastroenterology,01815667799,4.8,Dhaka
depression,Dr. Rukhsar Jahan,Psychiatry,01926778800,4.7,Khulna
anxiety,Dr. Faisal Mahmud,Psychiatry,01537889911,4.9,Barishal
kidney stone,Dr. Shaila Parvin,Urology,01748990022,4.8,Chattogram
liver disease,Dr. Noman Hossain,Hepatology,01859001133,4.7,Sylhet
mathabetha,Anisul Hauqe,Pagology,0987654,3.4,Bogura

```
