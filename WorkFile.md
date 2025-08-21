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
#include <fstream>
#include <sstream>
#include <vector>
#include <string>
#include <algorithm>
#include <limits>

using namespace std;

// ===============================
// Doctor class (simple version)
// ===============================
class Doctor {
private:
    string problem;
    string name;
    string department;
    string contact;
    double rating;
    string location;

public:
    // Default constructor
    Doctor() : rating(0.0) {}

    // Parameterized constructor
    Doctor(string prob, string n, string dept, string cont, double r, string loc)
        : problem(prob), name(n), department(dept), contact(cont), rating(r), location(loc) {}

    // Display doctor details
    void display() const {
        cout << "-----------------------------------------\n";
        cout << "Name      : " << name       << "\n";
        cout << "Department: " << department << "\n";
        cout << "Contact   : " << contact    << "\n";
        cout << "Rating    : " << rating     << "\n";
        cout << "Location  : " << location   << "\n";
    }

    // Convert to CSV format
    string toCSV() const {
        ostringstream oss;
        oss << problem << "," << name << "," << department << ","
            << contact << "," << rating << "," << location;
        return oss.str();
    }

    // Getters
    string getProblem() const { return problem; }
    string getName() const { return name; }
    double getRating() const { return rating; }
};

// ===============================
// Load doctors from CSV file
// ===============================
vector<Doctor> loadDoctors(const string& filename) {
    vector<Doctor> doctors;
    ifstream file(filename);
    if (!file.is_open()) return doctors;

    string line;
    getline(file, line); // skip header

    while (getline(file, line)) {
        stringstream ss(line);
        string prob, name, dept, contact, ratingStr, loc;
        getline(ss, prob, ',');
        getline(ss, name, ',');
        getline(ss, dept, ',');
        getline(ss, contact, ',');
        getline(ss, ratingStr, ',');
        getline(ss, loc, ',');

        double rating = stod(ratingStr);
        doctors.emplace_back(prob, name, dept, contact, rating, loc);
    }
    return doctors;
}

// ===============================
// Save doctors to CSV file
// ===============================
void saveDoctors(const string& filename, const vector<Doctor>& doctors) {
    ofstream file(filename);
    file << "Problem,Name,Department,Contact,Rating,Location\n";
    for (const auto& d : doctors) {
        file << d.toCSV() << "\n";
    }
}

// ===============================
// Show all doctors
// ===============================
void showAll(const vector<Doctor>& doctors) {
    if (doctors.empty()) {
        cout << "No doctors available.\n";
        return;
    }
    for (const auto& d : doctors) {
        d.display();
    }
}

// ===============================
// Add a new doctor
// ===============================
void addDoctor(vector<Doctor>& doctors) {
    string prob, name, dept, contact, loc;
    double rating;

    cout << "Enter problem keyword : ";
    getline(cin, prob);
    cout << "Enter full name       : ";
    getline(cin, name);
    cout << "Enter department      : ";
    getline(cin, dept);
    cout << "Enter contact number  : ";
    getline(cin, contact);

    cout << "Enter rating (0–5)    : ";
    while (!(cin >> rating) || rating < 0 || rating > 5) {
        cout << "Invalid rating. Enter again (0–5): ";
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
    }
    cin.ignore();

    cout << "Enter location        : ";
    getline(cin, loc);

    doctors.emplace_back(prob, name, dept, contact, rating, loc);
    cout << "Doctor added successfully.\n";
}

// ===============================
// Remove a doctor by name
// ===============================
void removeDoctor(vector<Doctor>& doctors) {
    cout << "Enter exact doctor name to remove: ";
    string target;
    getline(cin, target);

    auto it = remove_if(doctors.begin(), doctors.end(),
                        [&](const Doctor& d) { return d.getName() == target; });

    if (it == doctors.end()) {
        cout << "No doctor named \"" << target << "\" found.\n";
    } else {
        doctors.erase(it, doctors.end());
        cout << "Doctor \"" << target << "\" removed.\n";
    }
}

// ===============================
// Recommend doctors based on problem
// ===============================
void recommend(const vector<Doctor>& doctors) {
    cout << "Describe your medical problem: ";
    string complaint;
    getline(cin, complaint);

    vector<Doctor> matches;
    for (const auto& d : doctors) {
        string key = d.getProblem();
        string cLower = complaint, kLower = key;
        transform(cLower.begin(), cLower.end(), cLower.begin(), ::tolower);
        transform(kLower.begin(), kLower.end(), kLower.begin(), ::tolower);
        if (cLower.find(kLower) != string::npos) {
            matches.push_back(d);
        }
    }

    if (matches.empty()) {
        cout << "No doctors found for \"" << complaint << "\".\n";
        return;
    }

    sort(matches.begin(), matches.end(),
         [](const Doctor& a, const Doctor& b) { return a.getRating() > b.getRating(); });

    cout << "\n=== Recommended Doctors ===\n";
    for (const auto& d : matches) {
        d.display();
    }
}

// ===============================
// Main program
// ===============================
int main() {
    const string CSV_FILE = "doctors.csv";
    vector<Doctor> doctors = loadDoctors(CSV_FILE);

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

        while (true) {
            cout << "\nCommands: show | add | remove | save | exit\n> ";
            string cmd;
            getline(cin, cmd);
            transform(cmd.begin(), cmd.end(), cmd.begin(), ::tolower);

            if (cmd == "show") showAll(doctors);
            else if (cmd == "add") addDoctor(doctors);
            else if (cmd == "remove") removeDoctor(doctors);
            else if (cmd == "save") saveDoctors(CSV_FILE, doctors);
            else if (cmd == "exit") { saveDoctors(CSV_FILE, doctors); break; }
            else cout << "Unknown command.\n";
        }
    } else {
        recommend(doctors);
    }

    return 0;
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

