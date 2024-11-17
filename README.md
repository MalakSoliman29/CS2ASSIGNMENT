Patient Management System
This project is a simple Patient Management System implemented in C++. The system is designed to simulate a hospital queue system, prioritizing patients based on their urgency level (Urgent/Normal) and managing their waiting times.

Features
Add patients with attributes:
ID (14-digit validation)
Gender (M/F)
Arrival Time (in HH
format)
Type (Urgent or Normal)
Separate queues for Urgent and Normal cases.

Automatic prioritization of patients:
Urgent cases are served first.
Normal cases are served only when the Urgent queue is empty.
Incremental waiting time simulation for patients.
Display real-time queue statuses.

Summarize statistics:
Total patients served.
Count of urgent and normal cases.
Average waiting time for all patients.
Technologies Used
Language: C++
Standard Libraries: iostream, string, regex, queue, vector, stdexcept

Class Structure

1. Patient Class
Manages patient attributes and methods:
Attributes:
id: Unique 14-digit patient ID.
gender: Gender (M/F).
arrivalTime: Patient's arrival time (HH
format).
type: Patient type (Urgent/Normal).
waitTime: Total time the patient has waited.
Methods:
validateID: Validates the ID format using a regex pattern.
incrementWaitTime: Increments the patient's wait time.
getDetails: Displays patient details.

2. Queue Class
Implements separate queues for Urgent and Normal patients:
Methods:
enqueue: Adds a patient to the appropriate queue.
dequeue: Removes and returns the next patient to be served.
getUrgentPatients and getNormalPatients: Returns the lists of patients in respective queues.
isEmpty: Checks if both queues are empty.

3. Scheduler Class
Handles overall patient flow and statistics:

Attributes:
patients: A vector of all patients.
queue: A Queue object to manage urgent and normal queues.
Counters: totalPatients, urgentCount, normalCount, and totalWaitTime.
Methods:
addPatient: Adds a patient to the system.
dispatchPatients: Transfers all patients into the queue.
servePatients: Serves the next patient in the queue.
incrementWaitTimes: Updates the wait times for all queued patients.
displayStatus: Displays the current queue status.
displaySummary: Shows a summary of the simulation.

Input usage
Enter details for multiple patients:

Patient ID: Enter a 14-digit unique number.
Gender: Enter M for male or F for female.
Arrival Time: Enter time in HH:MM format.
Type: Enter Urgent or Normal.
Dispatch the patients into the queue.

Press Enter to simulate the passage of time (1 minute at a time).

Observe the queue status and served patients in real-time.

Exit the simulation at any time to view the summary.

