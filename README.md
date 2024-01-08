# ATM-Interface-using-C-Cpp#
#include <iostream>
#include <fstream>
#include <string>
#include <vector>
using namespace std;
class Atm
{
private:
    string names[5] = {"Raj", "Rohit", "Virat", "Riya", "Shikhar"};
    int storedId[5] = {2311, 2312, 2313, 2314, 2315};
    int storedPin[5] = {50, 20, 30, 10, 90};
    int amount[5] = {2000, 3000, 5000, 7000, 10000};
    vector<vector<string>> transactionHistory;
    void updateDatabase(int userId, int newBalance)
    {
        for (int i=0;i<5;i++)
        {
            if (storedId[i]==userId)
            {
                amount[i]=newBalance;
                break;
            }
        }
    }
    void saveDatabase()
    {
        ofstream file("database.txt");
        if (!file.is_open())
        {
            cout<<"Error: Unable to open database file for writing." << endl;
            return;
        }
        for (int i=0;i<5;i++)
        {
            file<<storedId[i]<<" "<<amount[i]<<endl;
        }
        file.close();
    }
    void loadDatabase()
    {
        ifstream file("database.txt");
        if (!file.is_open())
        {
            cout<<"Error: Unable to open database file for reading."<<endl;
            return;
        }
        for (int i=0;i<5;i++)
        {
            file>>storedId[i]>>amount[i];
        }
        file.close();
    }
public:
    Atm()
    {
        transactionHistory.resize(5);
    }
    void run()
    {
        loadDatabase();
        int userId, pin, flag = 0, x;
        cout << "~~~~~~~~WELCOME~~~~~~~~~~" << endl;
        cout << "Enter your userID: ";
        cin >> userId;
        cout << "Enter your pin: ";
        cin >> pin;
        for (int i=0;i<5;i++)
        {
            if (storedId[i]==userId && storedPin[i]==pin)
            {
                flag = 1;
                x = i;
                break;  
                }
        }
        if (flag == 1)
        {
            cout << "Welcome Mr." << names[x] << " To XYZ Bank" << endl;
            int choice;
            string var;
            bool isRunning = true;
            while (isRunning)
            {
                cout<< "\nKindly select from the options below:" << endl;
                cout<<"1.Transactions History"<<endl;
                cout<<"2.Withdraw Money"<<endl;
                cout<<"3.Deposit Money"<<endl;
                cout<<"4.Transfer Money"<<endl;
                cout<<"5.Quit"<<endl;
                cout<<"\nYour Current process:";
                cin>>choice;
                switch (choice)
                {
                case 1:
                    showTransactionHistory(userId);
                    break;
                case 2:
                    int valuew;
                    cout << "Enter amount to be withdrawn: ";
                    cin >> valuew;
                    withdraw(x, valuew);
                    break;
                case 3:
                    int valuede;
                    cout << "Enter amount to be deposited: ";
                    cin >> valuede;
                    deposit(x, valuede);
                    break;
                case 4:
                    int valuetrf, transfacc;
                    cout << "Enter amount to be transferred: ";
                    cin >> valuetrf;
                    cout << "Enter account to transfer money: ";
                    cin >> transfacc;
                    int y;
                    for (int i=0;i<5;i++)
                    {
                        if (storedId[i]==transfacc)
                        {
                            y=i;
                            break;
                        }
                    }
                    transfer(x,valuetrf,y);
                    break;
                case 5:
                    saveDatabase();
                    exit(1);
                    break;
                default:
                    cout << "Invalid choice,Please select from the above options"<<endl;
                    break;
                }
            }
        }
        else
        {
            cout<<"ERROR:Wrong userID or PIN"<<endl;
            cout<<"Kindly enter the correct details"<<endl;
        }
    }
    void showTransactionHistory(int userId)
    {
        int userIndex = -1;
        for (int i=0;i<5;i++)
        {
            if (storedId[i]==userId)
            {
                userIndex = i;
                break;
            }
        }
        if (userIndex!= -1)
        {
            if (transactionHistory[userIndex].empty())
            {
                cout<<"No transaction history available for Account Number"<<userId<< "."<<endl;
            }
            else
            {
                cout<<"Transaction History for Account Number "<<userId<<":"<<endl;
                for (const string &transaction : transactionHistory[userIndex])
                {
                    cout<<transaction<<endl;
                }
            }
        }
        else
        {
            cout<<"Account Number"<<userId<<"not found in the database"<<endl;
        }
    }
    void deposit(int j,int amt)
    {
        amount[j] =amount[j]+amt;
        cout<<"Amount successfully deposited:"<<amt<<"INR"<<endl;
        cout<<"Remaining balance in account is:"<<amount[j]<<"INR"<<endl;
        updateDatabase(storedId[j], amount[j]);
        string transactiond="Deposit of INR "+to_string(amt);
        transactionHistory[j].push_back(transactiond);
    }
    void withdraw(int k, int amt)
    {
        if (amount[k] >= amt)
        {
            amount[k] = amount[k] - amt;
            cout << "Amount successfully withdrawn: " << amt << " INR" << endl;
            cout << "Remaining balance in account is: " << amount[k] << " INR" << endl;
            updateDatabase(storedId[k], amount[k]);
            string transactionw = "Withdrawal of INR "+to_string(amt)+".";
            transactionHistory[k].push_back(transactionw);
        }
        else
        {
            cout<<"Insufficient balance to withdraw"<<endl;
        }
    }
    void transfer(int l,int amt,int m)
    {
        if (amount[l]>=amt)
        {
            amount[l]=amount[l]-amt;
            amount[m]=amount[m]+amt;
            cout<<"Amount successfully transferred" << endl;
            cout<<"Remaining balance in sender's account is:"<<amount[l]<<endl;
            cout<<"Remaining balance in receiver's account is:"<<amount[m]<<endl;
            updateDatabase(storedId[l],amount[l]);
            updateDatabase(storedId[m],amount[m]);
            string transactiont="Transferred"+to_string(amt)+"INR to "+to_string(storedId[m])+"\n"+"Received"+to_string(amt)+"INR from"+to_string(storedId[l]);
            transactionHistory[l].push_back("Transferred"+to_string(amt)+"INR to "+to_string(storedId[m]));
            transactionHistory[m].push_back("Received"+to_string(amt)+"INR from "+to_string(storedId[l]));
        }
        else
        {
          cout<<"Insufficient balance to transfer"<<endl;
        }
    }
};
int main()
{
    Atm at;
    at.run();
}
