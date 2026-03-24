# oslab3080
Operating System Lab exercise
# oslab3068
Operating System Lab exercise


ex 3
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t child_pid;

    child_pid = fork();

    if (child_pid < 0) {
        perror("Fork failed");
        return 1;
    }
    else if (child_pid == 0) {
        // Child
        printf("Child process: PID=%d\n", getpid());

        execl("/bin/ls", "ls", "-l", NULL);

        perror("execl failed");
        return 1;
    }
    else {
        // Parent
        printf("Parent process: PID=%d\n", getpid());
        wait(NULL);
        printf("Child process finished.\n");
    }

    return 0;
}


ex 4a

#include <stdio.h>

int main() {
    int n, i;
    int burst[10], wait[10], turn[10];
    float avg_w = 0, avg_t = 0;

    printf("Enter the number of processes: ");
    scanf("%d", &n);

    printf("Enter the burst time for each process:\n");
    for (i = 0; i < n; i++)
        scanf("%d", &burst[i]);

    wait[0] = 0;
    for (i = 1; i < n; i++)
        wait[i] = wait[i - 1] + burst[i - 1];

    for (i = 0; i < n; i++)
        turn[i] = wait[i] + burst[i];

    printf("\4bnGantt Chart:\n|");
    for (i = 0; i < n; i++)
        printf(" P%d |", i + 1);

    printf("\n0");
    for (i = 0; i < n; i++)
        printf("   %d", turn[i]);

    for (i = 0; i < n; i++) {
        avg_w += wait[i];
        avg_t += turn[i];
    }

    printf("\nAverage Waiting Time: %.2f", avg_w / n);
    printf("\nAverage Turnaround Time: %.2f", avg_t / n);

    return 0;
}


ex 4b
#include <stdio.h>

struct process {
    int id;
    int bt;      // Burst Time
    int wt;      // Waiting Time
    int tat;     // Turnaround Time
    int pr;      // Priority
};

void priorityScheduling(struct process p[], int n)
{
    struct process temp;

    // Sort by priority (smaller number = higher priority)
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (p[i].pr > p[j].pr) {
                temp = p[i];
                p[i] = p[j];
                p[j] = temp;
            }
        }
    }

    // Waiting time
    p[0].wt = 0;
    for (int i = 1; i < n; i++)
        p[i].wt = p[i-1].bt + p[i-1].wt;

    // Turnaround time
    for (int i = 0; i < n; i++)
        p[i].tat = p[i].wt + p[i].bt;

    float avgWT = 0, avgTAT = 0;

    printf("\nProcess\tBT\tPriority\tWT\tTAT\n");

    for (int i = 0; i < n; i++) {
        printf("P%d\t%d\t%d\t\t%d\t%d\n",
               p[i].id, p[i].bt, p[i].pr, p[i].wt, p[i].tat);

        avgWT += p[i].wt;
        avgTAT += p[i].tat;
    }

    printf("\nAverage Waiting Time = %.2f", avgWT/n);
    printf("\nAverage Turnaround Time = %.2f\n", avgTAT/n);
}

int main()
{
    int n;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    struct process p[n];

    for (int i = 0; i < n; i++) {
        p[i].id = i + 1;
        printf("\nEnter Burst Time for P%d: ", i+1);
        scanf("%d", &p[i].bt);
        printf("Enter Priority for P%d: ", i+1);
        scanf("%d", &p[i].pr);
    }

    priorityScheduling(p, n);

    return 0;
}

4c
#include <stdio.h>

struct process {
    int id;
    int bt;      // Burst Time
    int wt;      // Waiting Time
    int tat;     // Turnaround Time
    int pr;      // Priority
};

void priorityScheduling(struct process p[], int n)
{
    struct process temp;

    // Sort by priority (smaller number = higher priority)
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (p[i].pr > p[j].pr) {
                temp = p[i];
                p[i] = p[j];
                p[j] = temp;
            }
        }
    }

    // Waiting time
    p[0].wt = 0;
    for (int i = 1; i < n; i++)
        p[i].wt = p[i-1].bt + p[i-1].wt;

    // Turnaround time
    for (int i = 0; i < n; i++)
        p[i].tat = p[i].wt + p[i].bt;

    float avgWT = 0, avgTAT = 0;

    printf("\nProcess\tBT\tPriority\tWT\tTAT\n");

    for (int i = 0; i < n; i++) {
        printf("P%d\t%d\t%d\t\t%d\t%d\n",
               p[i].id, p[i].bt, p[i].pr, p[i].wt, p[i].tat);

        avgWT += p[i].wt;
        avgTAT += p[i].tat;
    }

    printf("\nAverage Waiting Time = %.2f", avgWT/n);
    printf("\nAverage Turnaround Time = %.2f\n", avgTAT/n);
}

int main()
{
    int n;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    struct process p[n];

    for (int i = 0; i < n; i++) {
        p[i].id = i + 1;
        printf("\nEnter Burst Time for P%d: ", i+1);
        scanf("%d", &p[i].bt);
        printf("Enter Priority for P%d: ", i+1);
        scanf("%d", &p[i].pr);
    }

    priorityScheduling(p, n);

    return 0;
}


ex 4d
#include <stdio.h>

void findWaitingTime(int bt[], int wt[], int n, int q)
{
    int rem_bt[n];

    for (int i = 0; i < n; i++)
        rem_bt[i] = bt[i];

    int t = 0;

    while (1) {
        int done = 1;

        for (int i = 0; i < n; i++) {
            if (rem_bt[i] > 0) {
                done = 0;

                if (rem_bt[i] > q) {
                    t += q;
                    rem_bt[i] -= q;
                } else {
                    t += rem_bt[i];
                    wt[i] = t - bt[i];
                    rem_bt[i] = 0;
                }
            }
        }

        if (done == 1)
            break;
    }
}

int main()
{
    int n, q;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    int bt[n], wt[n], tat[n];

    printf("Enter burst times:\n");
    for (int i = 0; i < n; i++) {
        printf("P%d: ", i+1);
        scanf("%d", &bt[i]);
    }

    printf("Enter time quantum: ");
    scanf("%d", &q);

    findWaitingTime(bt, wt, n, q);

    float avgWT = 0, avgTAT = 0;

    printf("\nProcess\tBT\tWT\tTAT\n");

    for (int i = 0; i < n; i++) {
        tat[i] = bt[i] + wt[i];

        printf("P%d\t%d\t%d\t%d\n",
               i+1, bt[i], wt[i], tat[i]);

        avgWT += wt[i];
        avgTAT += tat[i];
    }

    printf("\nAverage Waiting Time = %.2f", avgWT/n);
    printf("\nAverage Turnaround Time = %.2f\n", avgTAT/n);

    return 0;
