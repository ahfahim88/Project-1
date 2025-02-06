#include <iostream>
#include <vector>
#include <string>
#include <curl/curl.h>
using namespace std;

struct Book {
    int id;
    string title;
    string author;
    bool isAvailable;
};

vector<Book> books;

void saveToFirebase(const Book& book) {
    CURL *curl = curl_easy_init();
    if (curl) {
        string url = "https://your-firebase-url.firebaseio.com/books.json";
        string json = "{\"id\": " + to_string(book.id) + ", \"title\": \"" + book.title + "\", \"author\": \"" + book.author + "\", \"isAvailable\": " + (book.isAvailable ? "true" : "false") + "}";

        curl_easy_setopt(curl, CURLOPT_URL, url.c_str());
        curl_easy_setopt(curl, CURLOPT_POSTFIELDS, json.c_str());
        curl_easy_setopt(curl, CURLOPT_POST, 1);
        curl_easy_perform(curl);
        curl_easy_cleanup(curl);
    }
}

void addBook() {
    Book book;
    cout << "Enter Book ID: ";
    cin >> book.id;
    cin.ignore();
    cout << "Enter Title: ";
    getline(cin, book.title);
    cout << "Enter Author: ";
    getline(cin, book.author);
    book.isAvailable = true;
    books.push_back(book);
    saveToFirebase(book);
    cout << "Book added and saved online!\n";
}

void viewBooks() {
    for (const auto& book : books) {
        cout << "ID: " << book.id << " | Title: " << book.title << " | Author: " << book.author << " | "
             << (book.isAvailable ? "Available" : "Borrowed") << "\n";
    }
}

void searchBook() {
    string keyword;
    cout << "Enter title or author to search: ";
    cin.ignore();
    getline(cin, keyword);
    for (const auto& book : books) {
        if (book.title.find(keyword) != string::npos || book.author.find(keyword) != string::npos) {
            cout << "ID: " << book.id << " | Title: " << book.title << " | Author: " << book.author << " | "
                 << (book.isAvailable ? "Available" : "Borrowed") << "\n";
        }
    }
}

int main() {
    int choice;
    do {
        cout << "\nE-Library System\n";
        cout << "1. Add Book\n2. View Books\n3. Search Book\n4. Exit\nEnter choice: ";
        cin >> choice;
        switch (choice) {
            case 1: addBook(); break;
            case 2: viewBooks(); break;
            case 3: searchBook(); break;
            case 4: cout << "Exiting...\n"; break;
            default: cout << "Invalid choice!\n";
        }
    } while (choice != 4);
    return 0;
}
