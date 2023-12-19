#include <iostream>
#include <string>
#include <list>

using namespace std;

// User class representing a user's details
class User {
public:
    string firstName;
    string lastName;
    string birthdate;

    User(const string& first, const string& last, const string& date)
        : firstName(first), lastName(last), birthdate(date) {}

    // Hash function for generating a unique identifier based on user details
    int generateHash() const {
        // A simple hash function for demonstration purposes
        // You may want to use a more robust hash function in a real-world scenario
        return hash<string>{}(firstName + lastName + birthdate);
    }
};

// HashNode class representing a node in the hash table
class HashNode {
public:
    int key;  // Unique identifier generated by the hash function
    User user; // User details
};

// HashTable class with chaining to handle collisions
class HashTable {
private:
    static const int TABLE_SIZE = 10;
    list<HashNode>* table;

public:
    HashTable() {
        table = new list<HashNode>[TABLE_SIZE];
    }

    ~HashTable() {
        delete[] table;
    }

    // Function to add a new user to the hash table
    void addUser(const User& user) {
        int key = user.generateHash() % TABLE_SIZE;
        HashNode newNode;
        newNode.key = key;
        newNode.user = user;

        table[key].push_back(newNode);
    }

    // Function to search for a user in the hash table
    void searchUser(const User& user) {
        int key = user.generateHash() % TABLE_SIZE;

        for (const auto& node : table[key]) {
            if (node.user.firstName == user.firstName &&
                node.user.lastName == user.lastName &&
                node.user.birthdate == user.birthdate) {
                cout << "User found with key " << key << "\n";
                return;
            }
        }

        cout << "User not found\n";
    }

    // Function to delete a user from the hash table
    void deleteUser(const User& user) {
        int key = user.generateHash() % TABLE_SIZE;

        auto& userList = table[key];
        auto it = find_if(userList.begin(), userList.end(), [&user](const HashNode& node) {
            return node.user.firstName == user.firstName &&
                   node.user.lastName == user.lastName &&
                   node.user.birthdate == user.birthdate;
        });

        if (it != userList.end()) {
            userList.erase(it);
            cout << "User deleted from key " << key << "\n";
        } else {
            cout << "User not found for deletion\n";
        }
    }
};

int main() {
    HashTable userTable;

    // Adding users to the hash table
    User user1("John", "Doe", "1990-01-15");
    User user2("Jane", "Smith", "1985-05-22");
    User user3("Alice", "Johnson", "1992-11-30");

    userTable.addUser(user1);
    userTable.addUser(user2);
    userTable.addUser(user3);

    // Searching for users
    User searchUser("Jane", "Smith", "1985-05-22");
    userTable.searchUser(searchUser);

    // Deleting a user
    User deleteUser("John", "Doe", "1990-01-15");
    userTable.deleteUser(deleteUser);

    return 0;
}

//end of hashing








//merge sort
#include <iostream>
using namespace std;
void merge(int arr[], int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;

    // Create temporary arrays
    int leftArr[n1];
    int rightArr[n2];

    // Copy data to temporary arrays leftArr[] and rightArr[]
    for (int i = 0; i < n1; i++)
        leftArr[i] = arr[left + i];
    for (int j = 0; j < n2; j++)
        rightArr[j] = arr[mid + 1 + j];

    // Merge the temporary arrays back into arr[left..right]
    int i = 0; // Initial index of first subarray
    int j = 0; // Initial index of second subarray
    int k = left; // Initial index of merged subarray

    while (i < n1 && j < n2) {
        if (leftArr[i] <= rightArr[j]) {
            arr[k] = leftArr[i];
            i++;
        } else {
            arr[k] = rightArr[j];
            j++;
        }
        k++;
    }

    // Copy the remaining elements of leftArr[], if there are any
    while (i < n1) {
        arr[k] = leftArr[i];
        i++;
        k++;
    }

    // Copy the remaining elements of rightArr[], if there are any
    while (j < n2) {
        arr[k] = rightArr[j];
        j++;
        k++;
    }
}

void mergeSort(int arr[], int left, int right) {
    if (left < right) {
        // Same as (left + right) / 2, but avoids overflow for large left and right
        int mid = left + (right - left) / 2;

        // Sort first and second halves
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);

        // Merge the sorted halves
        merge(arr, left, mid, right);
    }
}

int main() {
    const int arrSize = 6;
    int arr[arrSize] = {12, 11, 13, 5, 6, 7};

    cout << "Original array: ";
    for (int i = 0; i < arrSize; i++) {
        cout << arr[i] << " ";
    }
    cout << "\n";

    // Perform Merge Sort
    mergeSort(arr, 0, arrSize - 1);

    cout << "

















//quick sort

#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
using namespace std;

class StudentRecord {
public:
    int id;
    string name;
    double gpa;
    string department;

    StudentRecord(int _id, const string& _name, double _gpa, const string& _department)
        : id(_id), name(_name), gpa(_gpa), department(_department) {}
};

class StudentRecordOrganizer {
private:
    vector<StudentRecord> records;

    void quickSort(int low, int high) {
        if (low < high) {
            int pivotIndex = partition(low, high);
            quickSort(low, pivotIndex - 1);
            quickSort(pivotIndex + 1, high);
        }
    }

    int partition(int low, int high) {
        int pivot = records[high].id;
        int i = low - 1;

        for (int j = low; j <= high - 1; j++) {
            if (records[j].id < pivot) {
                i++;
                swap(records[i], records[j]);
            }
        }

        swap(records[i + 1], records[high]);
        return i + 1;
    }

public:
    void addRecord(const StudentRecord& record) {
        records.push_back(record);
    }

    void modifyRecord(int id, const string& newName, double newGPA, const string& newDepartment) {
        auto it = find_if(records.begin(), records.end(), [id](const StudentRecord& record) {
            return record.id == id;
        });

        if (it != records.end()) {
            it->name = newName;
            it->gpa = newGPA;
            it->department = newDepartment;
        } else {
            cout << "Record not found.\n";
        }
    }

    void removeRecord(int id) {
        auto it = remove_if(records.begin(), records.end(), [id](const StudentRecord& record) {
            return record.id == id;
        });

        if (it != records.end()) {
            records.erase(it, records.end());
        } else {
            cout << "Record not found.\n";
        }
    }

    void displaySortedRecords() {
        quickSort(0, records.size() - 1);

        cout << "Sorted Student Records:\n";
        for (const auto& record : records) {
            cout << "ID: " << record.id << ", Name: " << record.name
                 << ", GPA: " << record.gpa << ", Department: " << record.department << endl;
        }
    }
};

int main() {
    StudentRecordOrganizer organizer;

    // Adding sample records
    organizer.addRecord(StudentRecord(101, "John Doe", 3.8, "Computer Science"));
    organizer.addRecord(StudentRecord(102, "Jane Smith", 3.5, "Physics"));
    organizer.addRecord(StudentRecord(103, "Bob Johnson", 3.2, "Mathematics"));
    organizer.addRecord(StudentRecord(104, "Alice Brown", 3.9, "Chemistry"));

    // Displaying initial records
    cout << "Initial Student Records:\n";
    organizer.displaySortedRecords();

    // Modifying a record
    organizer.modifyRecord(102, "Jane Johnson", 3.7, "Biology");

    // Displaying records after modification
    cout << "\nStudent Records after Modification:\n";
    organizer.displaySortedRecords();

    // Removing a record
    organizer.removeRecord(103);

    // Displaying records after removal
    cout << "\nStudent Records after Removal:\n";
    organizer.displaySortedRecords();

    return 0;
}

//end of quick sort






















//avl
#include <iostream>
#include <string>
using namespace std;

class PlayerRecord {
public:
    string username;
    int score;
    int rank;

    PlayerRecord(const string& name, int s) : username(name), score(s), rank(0) {}
};

class AVLNode {
public:
    PlayerRecord player;
    int height;
    AVLNode* left;
    AVLNode* right;

    AVLNode(PlayerRecord p) : player(p), height(1), left(nullptr), right(nullptr) {}
};

class AVLTree {
private:
    AVLNode* root;

    int height(AVLNode* node) {
        return (node == nullptr) ? 0 : node->height;
    }

    int balanceFactor(AVLNode* node) {
        return (node == nullptr) ? 0 : height(node->left) - height(node->right);
    }

    AVLNode* rightRotate(AVLNode* y) {
        AVLNode* x = y->left;
        AVLNode* T2 = x->right;

        // Perform rotation
        x->right = y;
        y->left = T2;

        // Update heights
        y->height = 1 + max(height(y->left), height(y->right));
        x->height = 1 + max(height(x->left), height(x->right));

        return x;
    }

    AVLNode* leftRotate(AVLNode* x) {
        AVLNode* y = x->right;
        AVLNode* T2 = y->left;

        // Perform rotation
        y->left = x;
        x->right = T2;

        // Update heights
        x->height = 1 + max(height(x->left), height(x->right));
        y->height = 1 + max(height(y->left), height(y->right));

        return y;
    }

    AVLNode* insertNode(AVLNode* node, PlayerRecord player) {
        // Perform standard BST insertion
        if (node == nullptr)
            return new AVLNode(player);

        if (player.score < node->player.score)
            node->left = insertNode(node->left, player);
        else if (player.score > node->player.score)
            node->right = insertNode(node->right, player);
        else
            return node; // Duplicate scores are not allowed

        // Update height of current node
        node->height = 1 + max(height(node->left), height(node->right));

        // Get the balance factor
        int balance = balanceFactor(node);

        // Perform rotations if needed to balance the tree
        if (balance > 1 && player.score < node->left->player.score)
            return rightRotate(node);

        if (balance < -1 && player.score > node->right->player.score)
            return leftRotate(node);

        if (balance > 1 && player.score > node->left->player.score) {
            node->left = leftRotate(node->left);
            return rightRotate(node);
        }

        if (balance < -1 && player.score < node->right->player.score) {
            node->right = rightRotate(node->right);
            return leftRotate(node);
        }

        return node;
    }

    AVLNode* minValueNode(AVLNode* node) {
        AVLNode* current = node;
        while (current->left != nullptr)
            current = current->left;
        return current;
    }

    AVLNode* deleteNode(AVLNode* root, int score) {
        if (root == nullptr)
            return root;

        if (score < root->player.score)
            root->left = deleteNode(root->left, score);
        else if (score > root->player.score)
            root->right = deleteNode(root->right, score);
        else {
            if ((root->left == nullptr) || (root->right == nullptr)) {
                AVLNode* temp = root->left ? root->left : root->right;
                if (temp == nullptr) {
                    temp = root;
                    root = nullptr;
                } else {
                    *root = *temp;
                }
                delete temp;
            } else {
                AVLNode* temp = minValueNode(root->right);
                root->player = temp->player;
                root->right = deleteNode(root->right, temp->player.score);
            }
        }

        if (root == nullptr)
            return root;

        root->height = 1 + max(height(root->left), height(root->right));

        int balance = balanceFactor(root);

        if (balance > 1 && balanceFactor(root->left) >= 0)
            return rightRotate(root);

        if (balance > 1 && balanceFactor(root->left) < 0) {
            root->left = leftRotate(root->left);
            return rightRotate(root);
        }

        if (balance < -1 && balanceFactor(root->right) <= 0)
            return leftRotate(root);

        if (balance < -1 && balanceFactor(root->right) > 0) {
            root->right = rightRotate(root->right);
            return leftRotate(root);
        }

        return root;
    }

    void inOrderTraversal(AVLNode* node) {
        if (node != nullptr) {
            inOrderTraversal(node->left);
            cout << "Username: " << node->player.username
                 << ", Score: " << node->player.score
                 << ", Rank: " << node->player.rank << endl;
            inOrderTraversal(node->right);
        }
    }

public:
    AVLTree() : root(nullptr) {}

    void insertPlayer(PlayerRecord player) {
        root = insertNode(root, player);
    }

    void deletePlayer(int score) {
        root = deleteNode(root, score);
    }

    void printLeaderboard() {
        cout << "Leaderboard:\n";
        inOrderTraversal(root);
    }
};

int main() {
    AVLTree leaderboard;

    // Adding players to the leaderboard
    leaderboard.insertPlayer(PlayerRecord("Player1", 1500));
    leaderboard.insertPlayer(PlayerRecord("Player2", 1200));
    leaderboard.insertPlayer(PlayerRecord("Player3", 1800));
    leaderboard.insertPlayer(PlayerRecord("Player4", 1600));
    leaderboard.insertPlayer(PlayerRecord("Player5", 1400));

    // Printing the initial leaderboard
    cout << "Initial Leaderboard:\n";
    leaderboard.printLeaderboard();

    // Modifying scores
    leaderboard.insertPlayer(PlayerRecord("Player6", 1550));
    leaderboard.insertPlayer(PlayerRecord("Player7", 1300));
    leaderboard.insertPlayer(PlayerRecord("Player8", 1700));

    // Printing the updated leaderboard
    cout << "\nUpdated Leaderboard:\n";
    leaderboard.printLeaderboard();

    // Removing inactive players
    leaderboard.deletePlayer(1300);
    leaderboard.deletePlayer(1800);

    // Printing the final leaderboard
    cout << "\nFinal Leaderboard:\n";
    leaderboard.printLeaderboard();

    return 0;
}
//end of avl 





















//graphs

#include <iostream>
#include <vector>
#include <queue>

using namespace std;

class Graph {
private:
    int V; // Number of vertices
    vector<vector<int>> adjList; // Adjacency list

public:
    // Constructor to initialize the graph with a given number of vertices
    Graph(int vertices) : V(vertices), adjList(vertices) {}

    // Function to add an edge between two vertices
    void addEdge(int u, int v) {
        adjList[u].push_back(v);
        adjList[v].push_back(u); // Assuming an undirected graph
    }

    // Breadth-First Search to find the shortest path between two users
    vector<int> bfsShortestPath(int start, int end) {
        vector<int> distance(V, -1);
        queue<int> q;

        q.push(start);
        distance[start] = 0;

        while (!q.empty()) {
            int current = q.front();
            q.pop();

            for (int neighbor : adjList[current]) {
                if (distance[neighbor] == -1) {
                    q.push(neighbor);
                    distance[neighbor] = distance[current] + 1;
                }
            }
        }

        return distance;
    }

    // Depth-First Search to identify connected components
    void dfsConnectedComponents(int node, vector<bool>& visited, vector<int>& component) {
        visited[node] = true;
        component.push_back(node);

        for (int neighbor : adjList[node]) {
            if (!visited[neighbor]) {
                dfsConnectedComponents(neighbor, visited, component);
            }
        }
    }

    // Function to find connected components
    vector<vector<int>> findConnectedComponents() {
        vector<vector<int>> components;
        vector<bool> visited(V, false);

        for (int i = 0; i < V; ++i) {
            if (!visited[i]) {
                vector<int> component;
                dfsConnectedComponents(i, visited, component);
                components.push_back(component);
            }
        }

        return components;
    }

    // Function to suggest friends based on mutual connections using BFS
    vector<int> suggestFriends(int user) {
        vector<int> suggestedFriends;
        vector<bool> visited(V, false);

        queue<int> q;
        q.push(user);
        visited[user] = true;

        while (!q.empty()) {
            int current = q.front();
            q.pop();

            for (int neighbor : adjList[current]) {
                if (!visited[neighbor]) {
                    q.push(neighbor);
                    visited[neighbor] = true;

                    // Include mutual friends of the starting user
                    suggestedFriends.push_back(neighbor);

                    for (int mutualFriend : adjList[neighbor]) {
                        if (!visited[mutualFriend] && mutualFriend != user) {
                            suggestedFriends.push_back(mutualFriend);
                        }
                    }
                }
            }
        }

        return suggestedFriends;
    }

    // Function to find influencers (nodes with significant connections)
    vector<int> findInfluencers() {
        vector<int> influencers;
        const int THRESHOLD = 2; // Adjust threshold as needed

        for (int i = 0; i < V; ++i) {
            if (adjList[i].size() >= THRESHOLD) {
                influencers.push_back(i);
            }
        }

        return influencers;
    }
};

int main() {
    int numUsers;

    // Get the number of users from the user
    cout << "Enter the number of users in the social network: ";
    cin >> numUsers;

    // Create a social network graph with the specified number of users
    Graph socialNetwork(numUsers);

    // Menu for user interaction
    int choice;
    do {
        cout << "\nSocial Network Menu:\n";
        cout << "1. Add an edge between users\n";
        cout << "2. Find the shortest path between two users\n";
        cout << "3. Find connected components in the network\n";
        cout << "4. Suggest friends for a user\n";
        cout << "5. Find influencers in the network\n";
        cout << "0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1: {
                int user1, user2;
                cout << "Enter user IDs to add an edge (space-separated): ";
                cin >> user1 >> user2;
                socialNetwork.addEdge(user1, user2);
                cout << "Edge added between users " << user1 << " and " << user2 << "\n";
                break;
            }
            case 2: {
                int start, end;
                cout << "Enter user IDs to find the shortest path (space-separated): ";
                cin >> start >> end;
                vector<int> shortestPath = socialNetwork.bfsShortestPath(start, end);
                cout << "Shortest path from user " << start << " to user " << end << ": "
                     << shortestPath[end] << " hops\n";
                break;
            }
            case 3: {
                vector<vector<int>> components = socialNetwork.findConnectedComponents();
                cout << "Connected components in the network:\n";
                for (const auto& component : components) {
                    for (int node : component) {
                        cout << node << " ";
                    }
                    cout << "\n";
                }
                break;
            }
            case 4: {
                int user;
                cout << "Enter user ID to suggest friends: ";
                cin >> user;
                vector<int> suggestedFriends = socialNetwork.suggestFriends(user);
                cout << "Suggested friends for user " << user << ": ";
                for (int friendNode : suggestedFriends) {
                    cout << friendNode << " ";
                }
                cout << "\n";
                break;
            }
            case 5: {
                vector<int> influencers = socialNetwork.findInfluencers();
                cout << "Influencers in the network: ";
                for (int influencer : influencers) {
                    cout << influencer << " ";
                }
                cout << "\n";
                break;
            }
            case 0:
                cout << "Exiting the social network program.\n";
                break;
            default:
                cout << "Invalid choice. Please enter a valid option.\n";
        }
    } while (choice != 0);

    return 0;
}
