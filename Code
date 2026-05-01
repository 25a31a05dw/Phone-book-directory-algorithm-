#include <iostream>
#include <fstream>
#include <string>
#include <cstring>
#include <iomanip>

using namespace std;

#define nullptr NULL // Compatibility for older compilers

struct ContactData {
    char name[50];
    char phone[15];
};

struct Node {
    ContactData data;
    Node* next;
    Node* prev;
};

class Phonebook {
private:
    Node* head;

public:
    Phonebook() {
        head = NULL;
        loadFromBinary();
    }

    ~Phonebook() {
        saveToBinary();
        Node* curr = head;
        while (curr) {
            Node* next = curr->next;
            delete curr;
            curr = next;
        }
    }

    // Module 1: Sorted Insertion (Maintains A-Z)
    void addContact(string n, string p) {
        Node* newNode = new Node;
        strncpy(newNode->data.name, n.c_str(), 49);
        strncpy(newNode->data.phone, p.c_str(), 14);
        newNode->next = NULL; newNode->prev = NULL;

        if (!head || strcasecmp(newNode->data.name, head->data.name) < 0) {
            newNode->next = head;
            if (head) head->prev = newNode;
            head = newNode;
            return;
        }

        Node* curr = head;
        while (curr->next && strcasecmp(newNode->data.name, curr->next->data.name) > 0)
            curr = curr->next;

        newNode->next = curr->next;
        newNode->prev = curr;
        if (curr->next) curr->next->prev = newNode;
        curr->next = newNode;
    }

    // Module 2: Unified Search
    void unifiedSearch(string query) {
        Node* curr = head;
        bool found = false;
        cout << "\n--- Search Results ---" << endl;
        while (curr) {
            if (strcasecmp(curr->data.name, query.c_str()) == 0 || strcmp(curr->data.phone, query.c_str()) == 0) {
                cout << "Match: " << curr->data.name << " | " << curr->data.phone << endl;
                found = true;
            }
            curr = curr->next;
        }
        if (!found) cout << "No records found matching: " << query << endl;
    }

    // Module 3: Unified Delete
    void unifiedDelete(string query) {
        Node* curr = head;
        bool deleted = false;
        while (curr) {
            if (strcasecmp(curr->data.name, query.c_str()) == 0 || strcmp(curr->data.phone, query.c_str()) == 0) {
                if (curr->prev) curr->prev->next = curr->next;
                if (curr->next) curr->next->prev = curr->prev;
                if (curr == head) head = curr->next;
                
                Node* toDelete = curr;
                curr = curr->next;
                delete toDelete;
                deleted = true;
                continue; 
            }
            curr = curr->next;
        }
        if (deleted) cout << "Record(s) deleted.\n";
        else cout << "Record not found.\n";
    }

    // Module 4: Integrated Update (Name, Number, or Both)
    void updateContact(string query) {
        Node* curr = head;
        Node* matches[10];
        int matchCount = 0;

        while (curr && matchCount < 10) {
            if (strcasecmp(curr->data.name, query.c_str()) == 0 || strcmp(curr->data.phone, query.c_str()) == 0) {
                matches[matchCount++] = curr;
            }
            curr = curr->next;
        }

        if (matchCount == 0) {
            cout << "Contact not found.\n";
            return;
        }

        Node* target = (matchCount > 1) ? NULL : matches[0];
        if (matchCount > 1) {
            cout << "\nMultiple matches. Type the exact Phone Number to confirm: ";
            string confirm; getline(cin, confirm);
            for (int i = 0; i < matchCount; i++) {
                if (strcmp(matches[i]->data.phone, confirm.c_str()) == 0) target = matches[i];
            }
        }

        if (!target) { cout << "Invalid Selection.\n"; return; }

        cout << "\nUpdating: " << target->data.name << " | " << target->data.phone;
        cout << "\n1. Update Name\n2. Update Number\n3. Update Both\nChoice: ";
        int upChoice; cin >> upChoice; cin.ignore();

        string oldPhone = target->data.phone;
        string oldName = target->data.name;

        if (upChoice == 1) { // Change Name only
            string newName; cout << "New Name: "; getline(cin, newName);
            unifiedDelete(oldPhone); 
            addContact(newName, oldPhone);
            cout << "Name updated.\n";
        } 
        else if (upChoice == 2) { // Change Number only
            string newPhone; cout << "New Number: "; getline(cin, newPhone);
            strncpy(target->data.phone, newPhone.c_str(), 14);
            cout << "Number updated.\n";
        } 
        else if (upChoice == 3) { // Change Both
            string newName, newPhone;
            cout << "New Name: "; getline(cin, newName);
            cout << "New Number: "; getline(cin, newPhone);
            unifiedDelete(oldPhone); 
            addContact(newName, newPhone);
            cout << "Both updated.\n";
        } 
        else { cout << "Invalid selection.\n"; }
    }

    void displayAll() {
        Node* curr = head;
        if (!curr) { cout << "\nDirectory Empty.\n"; return; }
        cout << "\n" << left << setw(20) << "NAME" << setw(15) << "PHONE" << endl;
        cout << "-----------------------------------\n";
        while (curr) {
            cout << left << setw(20) << curr->data.name << setw(15) << curr->data.phone << endl;
            curr = curr->next;
        }
    }

    void showStats() {
        int count = 0; Node* curr = head;
        while (curr) { count++; curr = curr->next; }
        cout << "\nTotal Contacts: " << count << endl;
    }

    void saveToBinary() {
        ofstream outFile("phonebook.dat", ios::binary | ios::trunc);
        Node* curr = head;
        while (curr) {
            outFile.write((char*)&curr->data, sizeof(ContactData));
            curr = curr->next;
        }
    }

    void loadFromBinary() {
        ifstream inFile("phonebook.dat", ios::binary);
        if (!inFile) return;
        ContactData temp;
        while (inFile.read((char*)&temp, sizeof(ContactData))) addContact(temp.name, temp.phone);
    }
};

int main() {
    Phonebook myBook;
    int choice;
    string input, phone;

    do {
        cout << "\n--- PHONEBOOK DIRECTORY ---";
        cout << "\n1. Add Contact\n2. Search (Name/No)\n3. Update Contact\n4. Delete (Name/No)\n5. View All\n6. Stats\n7. Exit\nChoice: ";
        if (!(cin >> choice)) { cin.clear(); cin.ignore(1000, '\n'); continue; }
        cin.ignore();

        switch (choice) {
            case 1: cout << "Name: "; getline(cin, input); cout << "Phone: "; getline(cin, phone); myBook.addContact(input, phone); break;
            case 2: cout << "Search Query: "; getline(cin, input); myBook.unifiedSearch(input); break;
            case 3: cout << "Update Query: "; getline(cin, input); myBook.updateContact(input); break;
            case 4: cout << "Delete Query: "; getline(cin, input); myBook.unifiedDelete(input); break;
            case 5: myBook.displayAll(); break;
            case 6: myBook.showStats(); break;
            case 7: cout << "Exiting...\n"; break;
            default: cout << "Invalid Choice.\n";
        }
    } while (choice != 7);
    return 0;
}
