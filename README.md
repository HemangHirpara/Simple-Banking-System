# Simple-Banking-System
TCP/UDP Client-Server Banking Application

The objective of this program is to be able to maintain a server to
handle multiple client connections via TCP.

Server input: Server program will take a port number over 8K.
i.e ./bankingServer 17469

Client input: Client program will take a IP address/machine name
to connect to and the respective port number.
i.e ./bankingClient java.cs.rutgers.edu 17469

# Usage

1. The program goes through the starting dir, and creates a
thread for every file and directory it finds and sorts the file if it is a
valid csv 
2. Use the command line to run the program: 
    a.Use ‘make’ command to compile files into two executables,
       bankingServer and bankingClient
a. ./bankingServer <port number>
b. ./bankingClient <machine to connect to> <port number>


# Design

Structures Used:
//Structure is used for a Linked List of all client socket file
descriptors returned by accept
typedef struct all_sockets{
int socket_id;
struct all_sockets *next;
}all_sockets;
//Structure is used to pass sock file descriptors and socket
structure to a newly created thread via pthread_create
typedef struct pthread_arg_t {
int new_socket_fd;
struct sockaddr_in client_address;
} client;
//Structure to hold account information for each account created
by clients
typedef struct account {
char *account_name;
double balance;
int isInSession; // 1 if yes, 0 if no
int socket;
struct account *next;
} account;


# Functions

Server Functions:
1.void* connection_handler()
a.Function called by main thread to accept all connections and create a client thread to handle connection
2.void *pthread_routine(void *arg)
a.Function called by client thread to process client interaction with server
3.void checkInput(char* in, char** cmd, char** arg)
a.Helper function to check validity of client buffer and parse input
4.void signal_handler(int signal_number)
a.Signal function to catch SIGINT (ctrl+c) and shutdown clients and server
5.void timer_handler(int signum)
a.Signal function to catch timer and print out server diagnostics
Client Functions:
1.void* send_cmd(void* input_socket1)
a.Function called by first main thread to send all correct commands to server to process
2.void* recv_out(void* socket_fd1)
a.Function called by second main thread to receive all messages sent by server
3.void checkInput(char* in, char** cmd, char** arg)
a.Helper function to check validity of client buffer and parse input


# Testing

Testing for this project required us to test two programs, server
and client.
Testing covered basic operations
Checked for race conditions and thread safety


**Assumptions**
* We assumed clients connected and disconnected from iLab machines.
* Only valid commands are sent to the server from client-side
* Proper balance handling, i.e cannot withdraw more money than what is currently available.
* Assumed client will be shut down via quit command
* Assumed server will be shut down via ctrl+c command
* Use of mutexes and semaphores to protect shared data specifically around create command and printing server diagnostics.
* Semaphore was used as a binary semaphore where the initial count value is set to 1. Mutexes are used around create command to only allow one thread to create and account at time to avoid the creation of duplicates.


