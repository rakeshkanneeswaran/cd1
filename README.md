#include <iostream>
#include <stack>
#include <string>
#include <vector>
#include <iomanip>

using namespace std;

bool isReducible(string s) {
    return s == "id" || s == "E+E" || s == "E*E" || s == "(E)";
}

string reduce(string s) {
    return "E";
}

void printState(vector<string> stack, string input, string action) {
    cout << setw(30);
    for (const string &s : stack) cout << s;
    cout << setw(20) << input;
    cout << setw(20) << action << endl;
}

int main() {
    string input = "id+id*id";
    vector<string> stack;
    int i = 0;

    cout << left << setw(30) << "Stack" << setw(20) << "Input" << setw(20) << "Action" << endl;
    cout << string(70, '-') << endl;

    while (i < input.length()) {
        // Shift
        if (input.substr(i, 2) == "id") {
            stack.push_back("id");
            i += 2;
        } else {
            stack.push_back(string(1, input[i]));
            i++;
        }

        printState(stack, input.substr(i), "Shift");

        // Try to reduce after every shift
        bool reduced = true;
        while (reduced) {
            reduced = false;
            // Check last 3
            if (stack.size() >= 3) {
                string a = stack[stack.size()-1];
                string b = stack[stack.size()-2];
                string c = stack[stack.size()-3];
                string triple = c + b + a;
                if (isReducible(triple)) {
                    stack.erase(stack.end()-3, stack.end());
                    stack.push_back(reduce(triple));
                    printState(stack, input.substr(i), "Reduce by " + triple + " → E");
                    reduced = true;
                }
            }
            // Check last 1
            if (!reduced && stack.size() >= 1) {
                string a = stack.back();
                if (isReducible(a)) {
                    stack.pop_back();
                    stack.push_back(reduce(a));
                    printState(stack, input.substr(i), "Reduce by " + a + " → E");
                    reduced = true;
                }
            }
        }
    }

    if (stack.size() == 1 && stack[0] == "E")
        cout << "\nInput is successfully parsed." << endl;
    else
        cout << "\nError in parsing input." << endl;

    return 0;
}
