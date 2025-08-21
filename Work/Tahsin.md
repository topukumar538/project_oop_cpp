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
        cout << "\nDoctor Info:\n";
        cout << "Name      : " << name << "\n";
        cout << "Department: " << department << "\n";
        cout << "Contact   : " << contact << "\n";
        cout << "Rating    : " << rating << "\n";
        cout << "Location  : " << location << "\n";
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
            cout << "Error opening file.\n";
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
            vector<Doctor> doctors = it->second;
            sort(doctors.begin(), doctors.end(), [](const Doctor& a, const Doctor& b) {
                return a.getRating() > b.getRating();
            });

            for (const auto& doc : doctors) {
                doc.display();
            }
        } else {
            cout << "No doctors found for that problem.\n";
        }
    }
};

int main() {
    DoctorHunter hunter;
    hunter.loadFromCSV("doctors.csv");

    string input;
    cout << "Enter your medical problem: ";
    getline(cin, input);

    hunter.recommend(input);

    return 0;
}
`````

# Look how this version work then implement the version with the instructions given by mam. This version is base on user site
