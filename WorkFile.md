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
#include <cctype>

using namespace std;

//─── Doctor struct ──────────────────────────────────────────────────────────────
struct Doctor {
    string problem;
    string name;
    string department;
    string contact;
    double rating;
    string location;

    void display() const {
        cout << "-----------------------------------------\n";
        cout << "Name      : " << name       << "\n";
        cout << "Department: " << department << "\n";
        cout << "Contact   : " << contact    << "\n";
        cout << "Rating    : " << rating     << "\n";
        cout << "Location  : " << location   << "\n";
    }

    string toCSV() const {
        ostringstream oss;
        oss << problem << ","
            << name    << ","
            << department << ","
            << contact << ","
            << rating  << ","
            << location;
        return oss.str();
    }
};

//─── Load & Save Helpers ────────────────────────────────────────────────────────
vector<Doctor> loadDoctors(const string& filename) {
    vector<Doctor> docs;
    ifstream file(filename);
    if (!file.is_open()) return docs;

    string line;
    getline(file, line);  // skip header
    while (getline(file, line)) {
        stringstream ss(line);
        Doctor d;
        string ratingStr;

        getline(ss, d.problem, ',');
        getline(ss, d.name, ',');
        getline(ss, d.department, ',');
        getline(ss, d.contact, ',');
        getline(ss, ratingStr, ',');
        getline(ss, d.location, ',');

        d.rating = stod(ratingStr);
        docs.push_back(d);
    }
    return docs;
}

void saveDoctors(const string& filename, const vector<Doctor>& docs) {
    ofstream file(filename);
    file << "Problem,Name,Department,Contact,Rating,Location\n";
    for (const auto& d : docs) {
        file << d.toCSV() << "\n";
    }
}

//─── Admin Functions ───────────────────────────────────────────────────────────
void showAll(const vector<Doctor>& docs) {
    if (docs.empty()) {
        cout << "No doctors in list.\n";
        return;
    }
    cout << "\n=== All Registered Doctors ===\n";
    for (const auto& d : docs)
        d.display();
}

void addDoctor(vector<Doctor>& docs) {
    Doctor d;
    cout << "Enter problem keyword : ";
    getline(cin, d.problem);
    cout << "Enter full name      : ";
    getline(cin, d.name);
    cout << "Enter department     : ";
    getline(cin, d.department);
    cout << "Enter contact number : ";
    getline(cin, d.contact);
    cout << "Enter rating (0–5)   : ";
    cin  >> d.rating;
    cin.ignore();  // consume newline
    cout << "Enter location       : ";
    getline(cin, d.location);

    docs.push_back(d);
    cout << "✅ Doctor added.\n";
}

void removeDoctor(vector<Doctor>& docs) {
    cout << "Enter exact doctor name to remove: ";
    string target;
    getline(cin, target);

    auto it = remove_if(docs.begin(), docs.end(),
        [&](const Doctor& d){ return d.name == target; });

    if (it == docs.end()) {
        cout << "⚠️  No doctor named \"" << target << "\" found.\n";
    } else {
        docs.erase(it, docs.end());
        cout << "✅ Doctor \"" << target << "\" removed.\n";
    }
}

//─── User Recommendation ───────────────────────────────────────────────────────
void recommend(const vector<Doctor>& docs) {
    cout << "\nDescribe your medical problem (e.g., skin, heart, headache): ";
    string complaint;
    getline(cin, complaint);

    // lowercase for substring matching
    transform(complaint.begin(), complaint.end(), complaint.begin(),
              [](unsigned char c){ return tolower(c); });

    vector<Doctor> matches;
    for (const auto& d : docs) {
        string key = d.problem;
        transform(key.begin(), key.end(), key.begin(),
                  [](unsigned char c){ return tolower(c); });

        if (complaint.find(key) != string::npos) {
            matches.push_back(d);
        }
    }

    if (matches.empty()) {
        cout << "⚠️  No doctors found for \"" << complaint << "\".\n";
        return;
    }

    sort(matches.begin(), matches.end(),
         [](const Doctor& a, const Doctor& b){
             return a.rating > b.rating;
         });

    cout << "\n=== Recommended Doctors ===\n";
    for (const auto& d : matches)
        d.display();
}

//─── main() ─────────────────────────────────────────────────────────────────────
int main() {
    const string CSV = "doctors.csv";
    vector<Doctor> docs = loadDoctors(CSV);

    // Select mode
    cout << "Mode [user/admin]: ";
    string mode;
    getline(cin, mode);

    // Trim whitespace
    auto notSpace = [](char ch){ return !isspace(static_cast<unsigned char>(ch)); };
    mode.erase(mode.begin(),
               find_if(mode.begin(), mode.end(), notSpace));
    mode.erase(find_if(mode.rbegin(), mode.rend(), notSpace).base(),
               mode.end());

    // To lowercase
    transform(mode.begin(), mode.end(), mode.begin(),
              [](unsigned char c){ return tolower(c); });

    if (mode == "admin") {
        cout << "Enter admin password: ";
        string pwd;
        getline(cin, pwd);

        if (pwd != "123") {
            cout << "❌ Incorrect password. Exiting.\n";
            return 0;
        }

        // Admin command loop
        while (true) {
            cout << "\nCommands: show | add | remove | exit\n> ";
            string cmd;
            getline(cin, cmd);

            if (cmd == "show") {
                showAll(docs);
            }
            else if (cmd == "add") {
                addDoctor(docs);
                saveDoctors(CSV, docs);
            }
            else if (cmd == "remove") {
                removeDoctor(docs);
                saveDoctors(CSV, docs);
            }
            else if (cmd == "exit") {
                cout << "🔒 Admin session ended.\n";
                break;
            }
            else {
                cout << "❌ Unknown command.\n";
            }
        }
    }
    else {
        // User mode
        recommend(docs);
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

