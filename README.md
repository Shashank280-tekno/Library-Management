#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class Book {
private:
    int id;
    string title;
    string author;
    bool isIssued;

public:
    Book(int bId, string bTitle, string bAuthor)
        : id(bId), title(bTitle), author(bAuthor), isIssued(false) {}

    int getId() const { return id; }
    string getTitle() const { return title; }
    string getAuthor() const { return author; }
    bool getIsIssued() const { return isIssued; }

    void issueBook() { isIssued = true; }
    void returnBook() { isIssued = false; }

    void display() const {
        cout << "ID: " << id << " | Title: " << title 
             << " | Author: " << author 
             << " | Status: " << (isIssued ? "Issued" : "Available") << "\n";
    }
};

class Member {
private:
    int memberId;
    string name;

public:
    Member(int mId, string mName) : memberId(mId), name(mName) {}

    int getMemberId() const { return memberId; }
    string getName() const { return name; }

    void display() const {
        cout << "Member ID: " << memberId << " | Name: " << name << "\n";
    }
};

class Library {
private:
    vector<Book> books;
    vector<Member> members;

public:
    void addBook(int id, const string& title, const string& author) {
        books.emplace_back(id, title, author);
        cout << "--> Book added successfully.\n";
    }

    void addMember(int id, const string& name) {
        members.emplace_back(id, name);
        cout << "--> Member registered successfully.\n";
    }

    void issueBook(int bookId, int memberId) {
        auto bookIt = find_if(books.begin(), books.end(), [bookId](const Book& b) { return b.getId() == bookId; });
        auto memberIt = find_if(members.begin(), members.end(), [memberId](const Member& m) { return m.getMemberId() == memberId; });

        if (bookIt == books.end()) {
            cout << "--> Error: Book not found.\n";
            return;
        }
        if (memberIt == members.end()) {
            cout << "--> Error: Member not found.\n";
            return;
        }
        if (bookIt->getIsIssued()) {
            cout << "--> Error: Book is already issued.\n";
            return;
        }

        bookIt->issueBook();
        cout << "--> Book '" << bookIt->getTitle() << "' issued to " << memberIt->getName() << ".\n";
    }

    void returnBook(int bookId) {
        auto bookIt = find_if(books.begin(), books.end(), [bookId](const Book& b) { return b.getId() == bookId; });

        if (bookIt == books.end()) {
            cout << "--> Error: Book not found.\n";
            return;
        }
        if (!bookIt->getIsIssued()) {
            cout << "--> Error: Book was not issued.\n";
            return;
        }

        bookIt->returnBook();
        cout << "--> Book '" << bookIt->getTitle() << "' returned successfully.\n";
    }

    void searchByTitleOrAuthor(const string& query) const {
        cout << "\n--- Search Results ---\n";
        bool found = false;
        for (const auto& book : books) {
            if (book.getTitle().find(query) != string::npos || book.getAuthor().find(query) != string::npos) {
                book.display();
                found = true;
            }
        }
        if (!found) {
            cout << "No matching books found.\n";
        }
    }

    void displayAllBooks() const {
        cout << "\n--- Library Catalog ---\n";
        if (books.empty()) {
            cout << "No books available in the library.\n";
            return;
        }
        for (const auto& book : books) {
            book.display();
        }
    }
};

int main() {
    Library lib;
    int choice;

    while (true) {
        cout << "\n========== LIBRARY MENU ==========\n";
        cout << "1. Add Book\n";
        cout << "2. Add Member\n";
        cout << "3. Issue Book\n";
        cout << "4. Return Book\n";
        cout << "5. Search Book (Title/Author)\n";
        cout << "6. Display All Books\n";
        cout << "7. Exit\n";
        cout << "Enter your choice (1-7): ";
        
        if (!(cin >> choice)) {
            cin.clear();
            cin.ignore(10000, '\n');
            cout << "Invalid input. Please enter a number.\n";
            continue;
        }

        cin.ignore(10000, '\n'); // Clear input buffer

        if (choice == 1) {
            int id;
            string title, author;
            cout << "Enter Book ID: ";
            cin >> id;
            cin.ignore(10000, '\n');
            cout << "Enter Book Title: ";
            getline(cin, title);
            cout << "Enter Book Author: ";
            getline(cin, author);
            lib.addBook(id, title, author);

        } else if (choice == 2) {
            int id;
            string name;
            cout << "Enter Member ID: ";
            cin >> id;
            cin.ignore(10000, '\n');
            cout << "Enter Member Name: ";
            getline(cin, name);
            lib.addMember(id, name);

        } else if (choice == 3) {
            int bId, mId;
            cout << "Enter Book ID to issue: ";
            cin >> bId;
            cout << "Enter Member ID: ";
            cin >> mId;
            lib.issueBook(bId, mId);

        } else if (choice == 4) {
            int bId;
            cout << "Enter Book ID to return: ";
            cin >> bId;
            lib.returnBook(bId);

        } else if (choice == 5) {
            string query;
            cout << "Enter title or author search keyword: ";
            getline(cin, query);
            lib.searchByTitleOrAuthor(query);

        } else if (choice == 6) {
            lib.displayAllBooks();

        } else if (choice == 7) {
            cout << "Exiting system. Goodbye!\n";
            break;

        } else {
            cout << "Invalid option. Select 1 to 7.\n";
        }
    }

    return 0;
}
