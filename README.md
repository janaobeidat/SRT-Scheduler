#include<iostream>
#include<string>
#include<fstream>
using namespace std;  
struct Process {
    string name;
    int arrival_time;
    int processing_time;
    int remaining_time  ;
    int first_start;
    int id;
};
void SRTScheduler(Process processes[], int n ,ofstream& outFile) {
    int currentTime = 0;
    int completed = 0;
    int shortest = -1; 
    int timeQuantum = 1; // for SRT, time quantum is 1
    string *seq=new string[n] ;
     int count=0; 
     for (int i = 0; i < n ; i++)
     {
         processes[i].remaining_time=processes[i].processing_time;
     }
    while (completed != n) {
        // Find the process with the shortest remaining time
        shortest=-1;
        for (int i = 0; i < n ; i++) {
           
            if (processes[i].arrival_time <= currentTime && processes[i].remaining_time > 0) {
                if (shortest == -1 || processes[i].remaining_time < processes[shortest].remaining_time) {
                    shortest = i;
                }
                else if(processes[i].remaining_time == processes[shortest].remaining_time && processes[i].arrival_time != processes[shortest].arrival_time )
                   { if(processes[i].arrival_time > processes[shortest].arrival_time){shortest = i;}
                   }
                else if(processes[i].remaining_time == processes[shortest].remaining_time && processes[i].arrival_time == processes[shortest].arrival_time )
                {
                    if(processes[i].id<processes[shortest].id) 
                    shortest=i;
                }
                
            }
        }//for
        

        if (shortest == -1) {  // to increment the ct if i dont have processes yet 
            currentTime++;
            continue;
        }
        // Execute the shortest remaining time process
        processes[shortest].remaining_time--;
            if(processes[shortest].processing_time - processes[shortest].remaining_time == 1)
            {
                 processes[shortest].first_start=currentTime;
            }
        currentTime++;
        // If the process is completed
        if (processes[shortest].remaining_time == 0) {
            completed++;
            seq[count++]= processes[shortest].name ;
            int turnaroundTime = currentTime  - processes[shortest].arrival_time;
            int delay = turnaroundTime - processes[shortest].processing_time;
            int response = processes[shortest].first_start - processes[shortest].arrival_time;
            outFile << processes[shortest].name <<": ( end="<<currentTime<< ", turnaround="  
            << turnaroundTime <<", delay="<< delay<<", response="<<response<<" )" <<endl;
            
            
        }
        
    }//while

    for(int i=0;i<n;i++){
    outFile<<seq[i];
    }
    delete []seq;
}//fun
int main() {
    ifstream inFile("in.txt");
    ofstream outFile("out.txt");

    if (!inFile || !outFile) {
        cerr << "Error opening files." << endl;
        return 1;
    }

    int numProcesses;
    inFile >> numProcesses;

    Process processes[numProcesses];
    for (int i = 0; i < numProcesses; i++) {
        inFile >> processes[i].name >> processes[i].arrival_time >> processes[i].processing_time;
        processes[i].id = i;
    }
    

   

    // Call SRT scheduler
    SRTScheduler(processes, numProcesses , outFile);

    inFile.close();
    outFile.close();
    return 0;
}
