````cpp
#include <iostream>
#include <fstream>
#include <sstream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

struct Doctor {
    string problem, name, department, contact, location;
    double rating;

    string toCSV() const {
        return problem + "," + name + "," + department + "," + contact + "," + to_string(rating) + "," + location;
    }

    void display() const {
        cout << "\nName      : " << name
             << "\nDepartment: " << department
             << "\nContact   : " << contact
             << "\nRating    : " << rating
             << "\nLocation  : " << location << "\n";
    }
};

vector<Doctor> loadDoctors(const string& filename) {
    vector<Doctor> doctors;
    ifstream file(filename);
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

        Doctor doc{problem, name, department, contact, location, stod(ratingStr)};
        doctors.push_back(doc);
    }
    file.close();
    return doctors;
}

void saveDoctors(const string& filename, const vector<Doctor>& doctors) {
    ofstream file(filename);
    file << "Problem,Name,Department,Contact,Rating,Location\n";
    for (const auto& doc : doctors) {
        file << doc.toCSV() << "\n";
    }
    file.close();
}

void showAllDoctors(const vector<Doctor>& doctors) {
    for (const auto& doc : doctors) {
        doc.display();
    }
}

void removeDoctor(vector<Doctor>& doctors, const string& name) {
    auto it = remove_if(doctors.begin(), doctors.end(), [&](const Doctor& doc) {
        return doc.name == name;
    });

    if (it != doctors.end()) {
        doctors.erase(it, doctors.end());
        cout << "✅ Doctor '" << name << "' removed.\n";
    } else {
        cout << "⚠️ Doctor not found.\n";
    }
}

void addDoctor(vector<Doctor>& doctors) {
    Doctor newDoc;
    cout << "Enter problem: ";
    getline(cin, newDoc.problem);
    cout << "Enter name: ";
    getline(cin, newDoc.name);
    cout << "Enter department: ";
    getline(cin, newDoc.department);
    cout << "Enter contact: ";
    getline(cin, newDoc.contact);
    cout << "Enter rating: ";
    cin >> newDoc.rating;
    cin.ignore();
    cout << "Enter location: ";
    getline(cin, newDoc.location);

    doctors.push_back(newDoc);
    cout << "✅ Doctor added.\n";
}

int main() {
    string mode;
    cout << "Enter mode (admin/user): ";
    cin >> mode;
    cin.ignore();

    if (mode == "admin") {
        string password;
        cout << "Enter admin password: ";
        getline(cin, password);

        if (password != "123") {
            cout << "❌ Incorrect password.\n";
            return 0;
        }

        vector<Doctor> doctors = loadDoctors("doctors.csv");

        string command;
        cout << "Enter command (show/remove/add): ";
        getline(cin, command);

        if (command == "show") {
            showAllDoctors(doctors);
        } else if (command == "remove") {
            string name;
            cout << "Enter doctor name to remove: ";
            getline(cin, name);
            removeDoctor(doctors, name);
            saveDoctors("doctors.csv", doctors);
        } else if (command == "add") {
            addDoctor(doctors);
            saveDoctors("doctors.csv", doctors);
        } else {
            cout << "❌ Unknown command.\n";
        }
    } else {
        cout << "🔒 Access denied. Only admin can perform these actions.\n";
    }

    return 0;
}
````

# This code take admin as input then enter password 123 then the programme can show full list of doctors and add and remove doctor from the list. You need to understand the code then implement the code using hafsa mam instructions.
