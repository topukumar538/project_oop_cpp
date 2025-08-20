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

Below is a sample code snippet provided by Copilot.  
Please review it, and share your thoughts or improvements in your own `.md` file and in our Messenger group.

````c++

// Doctor class to hold details
#include <iostream>
#include <unordered_map>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

// Doctor class to hold details
class Doctor {
private:
    string name, department, contact, location;
    double rating;

public:
    Doctor(string n, string d, string c, double r, string l)
        : name(n), department(d), contact(c), rating(r), location(l) {}

    void display() const {
        cout << "→ " << name << " (" << department << ") — Rating: " << rating
             << " — Contact: " << contact << " — Location: " << location << "\n";
    }

    double getRating() const { return rating; }
};

// DoctorHunter class to manage recommendations
class DoctorHunter {
private:
    unordered_map<string, vector<Doctor>> problemMap;

public:
    DoctorHunter() {
        // Hardcoded doctor database
        problemMap["chest pain"] = {
            Doctor("Dr. Ayesha Rahman", "Cardiology", "017XXXXXXXX", 4.8, "Dhaka"),
            Doctor("Dr. Tanvir Islam", "Pulmonology", "018XXXXXXXX", 4.6, "Rajshahi")
        };
        problemMap["skin rash"] = {
            Doctor("Dr. Farhana Kabir", "Dermatology", "019XXXXXXXX", 4.9, "Chattogram"),
            Doctor("Dr. Imran Hossain", "Dermatology", "017YYYYYYYY", 4.7, "Sylhet")
        };
        problemMap["headache"] = {
            Doctor("Dr. Mahmud Hasan", "Neurology", "016XXXXXXXX", 4.7, "Sylhet")
        };
        problemMap["fever"] = {
            Doctor("Dr. Nusrat Jahan", "General Medicine", "015XXXXXXXX", 4.5, "Khulna")
        };
    }

    void recommend(const string& problem) {
        auto it = problemMap.find(problem);
        if (it != problemMap.end()) {
            cout << "\nRecommended Doctors for '" << problem << "':\n";
            vector<Doctor> doctors = it->second;

            // Sort by rating (optional)
            sort(doctors.begin(), doctors.end(), [](const Doctor& a, const Doctor& b) {
                return a.getRating() > b.getRating();
            });

            for (const auto& doc : doctors) {
                doc.display();
            }
        } else {
            cout << "Sorry, no doctors found for that problem.\n";
        }
    }
};

int main() {
    DoctorHunter hunter;
    string input;

    cout << "🩺 Welcome to Doctor Hunter!\n";
    cout << "Please enter your medical problem (e.g., chest pain, skin rash): ";
    getline(cin, input);

    hunter.recommend(input);

    return 0;
}

}
````



# csv version
`````cpp
#include <iostream>
#include <fstream>
#include <sstream>
#include <unordered_map>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class Doctor {
private:
    string name, department, contact, location;
    double rating;

public:
    Doctor(string n, string d, string c, double r, string l)
        : name(n), department(d), contact(c), rating(r), location(l) {}

    void display() const {
        cout << "→ " << name << " (" << department << ") — Rating: " << rating
             << " — Contact: " << contact << " — Location: " << location << "\n";
    }

    double getRating() const { return rating; }
};

class DoctorHunter {
private:
    unordered_map<string, vector<Doctor>> problemMap;

public:
    void loadFromCSV(const string& filename) {
        ifstream file(filename);
        if (!file.is_open()) {
            cerr << "Error: Could not open file " << filename << "\n";
            return;
        }

        string line;
        getline(file, line); // Skip header

        while (getline(file, line)) {
            stringstream ss(line);
            string problem, name, department, contact, ratingStr, location;

            getline(ss, problem, ',');
            getline(ss, name, ',');
            getline(ss, department, ',');
            getline(ss, contact, ',');
            getline(ss, ratingStr, ',');
            getline(ss, location, ',');

            double rating = stod(ratingStr);
            Doctor doc(name, department, contact, rating, location);
            problemMap[problem].push_back(doc);
        }

        file.close();
    }

    void recommend(const string& problem) {
        auto it = problemMap.find(problem);
        if (it != problemMap.end()) {
            cout << "\nRecommended Doctors for '" << problem << "':\n";
            vector<Doctor> doctors = it->second;

            sort(doctors.begin(), doctors.end(), [](const Doctor& a, const Doctor& b) {
                return a.getRating() > b.getRating();
            });

            for (const auto& doc : doctors) {
                doc.display();
            }
        } else {
            cout << "Sorry, no doctors found for that problem.\n";
        }
    }
};

int main() {
    DoctorHunter hunter;
    hunter.loadFromCSV("doctors.csv");

    string input;
    cout << "🩺 Welcome to Doctor Hunter!\n";
    cout << "Please enter your medical problem (e.g., chest pain, skin rash): ";
    getline(cin, input);

    hunter.recommend(input);

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

