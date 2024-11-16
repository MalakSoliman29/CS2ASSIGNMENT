#include <iostream>
#include <string>
#include <regex>
#include <queue>
#include <vector>
#include <stdexcept>
#include <cstdlib>
#include <ctime>

class Patient {
private:
    std::string id;
    char gender;
    std::string arrivalTime; // in HH:MM format
    std::string type; // "Urgent" or "Normal"
    int waitTime; // in minutes

public:
    Patient(std::string id, char gender, std::string arrivalTime, std::string type)
        : id(id), gender(gender), arrivalTime(arrivalTime), type(type), waitTime(0) {
        if (!validateID()) {
            throw std::invalid_argument("Invalid Patient ID format.");
        }
    }

    bool validateID() {
        std::regex idPattern("^[0-9]{14}$");
        return std::regex_match(id, idPattern);
    }

    void incrementWaitTime() { waitTime++; }

    void getDetails() const {
        std::cout << "ID: " << id << ", Gender: " << gender
            << ", Arrival Time: " << arrivalTime
            << ", Type: " << type << std::endl;
    }

    std::string getID() const { return id; }
    std::string getType() const { return type; }
    int getWaitTime() const { return waitTime; }
};

class Queue {
private:
    std::queue<Patient> urgentQueue;
    std::queue<Patient> normalQueue;

public:
    void enqueue(Patient patient) {
        if (patient.getType() == "Urgent") {
            urgentQueue.push(patient);
        }
        else {
            normalQueue.push(patient);
        }
    }

    Patient dequeue() {
        if (!urgentQueue.empty()) {
            Patient p = urgentQueue.front();
            urgentQueue.pop();
            return p;
        }
        else if (!normalQueue.empty()) {
            Patient p = normalQueue.front();
            normalQueue.pop();
            return p;
        }
        throw std::out_of_range("No patients to serve.");
    }

    bool isEmpty() const {
        return urgentQueue.empty() && normalQueue.empty();
    }

    std::vector<Patient> getUrgentPatients() const {
        std::vector<Patient> patients;
        std::queue<Patient> tempQueue = urgentQueue;
        while (!tempQueue.empty()) {
            patients.push_back(tempQueue.front());
            tempQueue.pop();
        }
        return patients;
    }

    std::vector<Patient> getNormalPatients() const {
        std::vector<Patient> patients;
        std::queue<Patient> tempQueue = normalQueue;
        while (!tempQueue.empty()) {
            patients.push_back(tempQueue.front());
            tempQueue.pop();
        }
        return patients;
    }
};

class Scheduler {
private:
    std::vector<Patient> patients;
    Queue queue;
    int totalPatients = 0;
    int urgentCount = 0;
    int normalCount = 0;
    int totalWaitTime = 0;

public:
    void addPatient(const Patient& patient) {
        patients.push_back(patient);
        totalPatients++;
        if (patient.getType() == "Urgent") {
            urgentCount++;
        }
        else {
            normalCount++;
        }
    }

    void dispatchPatients() {
        for (auto& patient : patients) {
            queue.enqueue(patient);
        }
        patients.clear(); // Clear vector after dispatching
    }

    void servePatients() {
        if (!queue.isEmpty()) {
            try {
                Patient patient = queue.dequeue();
                std::cout << "Done Serving Patient: " << patient.getID() << std::endl;
                totalWaitTime += patient.getWaitTime();
            }
            catch (std::out_of_range& e) {
                std::cout << e.what() << std::endl;
            }
        }
    }

    void incrementWaitTimes() {
        auto urgentPatients = queue.getUrgentPatients();
        for (auto& patient : urgentPatients) {
            patient.incrementWaitTime();
        }
        auto normalPatients = queue.getNormalPatients();
        for (auto& patient : normalPatients) {
            patient.incrementWaitTime();
        }
    }

    void displayStatus() const {
        std::cout << "Waiting Urgent Patient IDs: ";
        auto urgentPatients = queue.getUrgentPatients();
        for (const auto& patient : urgentPatients) {
            std::cout << patient.getID() << " ";
        }
        std::cout << "\nWaiting Normal Patient IDs: ";
        auto normalPatients = queue.getNormalPatients();
        for (const auto& patient : normalPatients) {
            std::cout << patient.getID() << " ";
        }
        std::cout << "\n---------------------------------\n";
    }

    void displaySummary() const {
        std::cout << "Total Patients: " << totalPatients << "\n";
        std::cout << "Urgent Cases: " << urgentCount << "\n";
        std::cout << "Normal Cases: " << normalCount << "\n";
        if (totalPatients > 0) {
            std::cout << "Average Waiting Time: " << static_cast<double>(totalWaitTime) / totalPatients << " minutes\n";
        }
    }
};

int main() {
    Scheduler scheduler;
    std::string id, arrivalTime, type;
    char gender;

    // Input patient data
    for (int i = 0; i < 3; ++i) { // Example: Add 5 patients
        std::cout << "Enter Patient ID: ";
        std::cin >> id;
        std::cout << "Enter Gender (M/F): ";
        std::cin >> gender;
        std::cout << "Enter Arrival Time (HH:MM): ";
        std::cin >> arrivalTime;
        std::cout << "Enter Type (Urgent/Normal): ";
        std::cin >> type;

        try {
            Patient patient(id, gender, arrivalTime, type);
            scheduler.addPatient(patient);
        }
        catch (const std::invalid_argument& e) {
            std::cerr << e.what() << std::endl;
        }
    }

    scheduler.dispatchPatients();

    // Simulation loop
    std::cout << "Press Enter to advance 1 minute...\n";
    while (true) {
        std::cin.get(); // Wait for user input to advance time

        scheduler.incrementWaitTimes();
        scheduler.displayStatus();
        scheduler.servePatients();
    }

    // Display summary at the end of the simulation
    scheduler.displaySummary();

    return 0;
}
