#include<iostream>
#include<string>
#include<fstream>

using namespace std;


class Doctor {
private:
    string problem;
    string name;
    string department;
    string contact;
    double rating;
    string location;

public:
    
    Doctor() : rating(0.0) {}

    
    Doctor(string prob, string n, string dept, string cont, double r, string loc)
        : problem(prob), name(n), department(dept), contact(cont), rating(r), location(loc) {}

    
    void display() const {

        ifstream ip("D:/Tahsin/Documents/1st year even/Project/doctors.csv");

        if(!ip.is_open()) {cout << "ERROR: File Open" << '\n';}

        

        while(ip.good()){
            string n;
            string d;
            string c;
            string r;
            string l;

            getline(ip,n,',');
            getline(ip,d,',');
            getline(ip,c,',');
            getline(ip,r,',');
            getline(ip,l,'\n');

            cout << "-----------------------------------------\n";
            cout << "Name      : " << n << "\n";
            cout << "Department: " << d << "\n";
            cout << "Contact   : " << c << "\n";
            cout << "Rating    : " << r << "\n";
            cout << "Location  : " << l << "\n";
            }

        ip.close();
    }
};

void showAll()
{
    Doctor o;
    o.display();

}

void addDoctor()
{
    int n;
    cout << "How many doctors do you want to add? ";
    cin >> n;
    ofstream aF("D:/Tahsin/Documents/1st year even/Project/doctors.csv", ios::app);

    for (int i = 0; i < n; i++) {
        string n;
        string d;
        string c;
        float r;
        string l;

        cout << "\nDoctor " << i+1 << " Name: ";
        getline(cin,n,' ');
        cout << "Department: ";
        getline(cin,d,' ');
        cout << "Contact: ";
        getline(cin,c,' ');
        cout << "Rating: ";
        cin >> r;
        cout << "Location: ";
        getline(cin,l,' ');


        aF << n << "," << d << "," << c <<","<< r <<"," << l << "\n";
    }

    aF.close();
}
int main()
{
    //ofstream file("D:/Tahsin/Documents/1st year even/Project/doctors.csv");
    //file << "Name,Age,Grade\n";
    //file.close();

    int login;
    cout<<"Login (1.Admin 2.User):"<<endl;
    cin>>login;

    if(login==1)
    {
        cout << "Enter admin password: ";
        //string password;
        int pin;
        cin>>pin;
        //getline(cin, password,' ');
        if (pin != 0000) {
            cout << "Incorrect password. Exiting.\n";
            return 0;
        }

        else
        {
            cout << "\nCommands: 1.show | 2.add | 3.remove | 4.save | 5.exit\n> ";
            int cmd;
            cin>>cmd;
            //transform(cmd.begin(), cmd.end(), cmd.begin(), ::tolower);

            if (cmd == 1) showAll();
            else if (cmd == 2) addDoctor();
            //else if (cmd == 3) removeDoctor(doctors);
            //else if (cmd == 4) saveDoctors(CSV_FILE, doctors);
            //else if (cmd == 5) { saveDoctors(CSV_FILE, doctors); break; }
           // else cout << "Unknown command.\n";
        }
    }

}
