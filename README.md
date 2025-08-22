#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <netinet/tcp.h> // For TCP options
#include <arpa/inet.h>   // For inet_ntoa

void print_ip_options(int sockfd, FILE *fp) {
    fprintf(fp, "--- IP Layer Options ---\n");

    int val;
    socklen_t len = sizeof(val);

    // IP_TTL
    if (getsockopt(sockfd, IPPROTO_IP, IP_TTL, &val, &len) == 0) {
        fprintf(fp, "IP_TTL: %d\n", val);
    } else {
        perror("getsockopt IP_TTL");
    }

    // IP_TOS (Type of Service)
    if (getsockopt(sockfd, IPPROTO_IP, IP_TOS, &val, &len) == 0) {
        fprintf(fp, "IP_TOS: %d\n", val);
    } else {
        perror("getsockopt IP_TOS");
    }

    // Add more IP options as needed (e.g., IP_HDRINCL, IP_RECVERR, etc.)
}

void print_tcp_options(int sockfd, FILE *fp) {
    fprintf(fp, "\n--- TCP Layer Options ---\n");

    int val;
    socklen_t len = sizeof(val);

    // TCP_NODELAY
    if (getsockopt(sockfd, IPPROTO_TCP, TCP_NODELAY, &val, &len) == 0) {
        fprintf(fp, "TCP_NODELAY: %d\n", val);
    } else {
        perror("getsockopt TCP_NODELAY");
    }

    // SO_KEEPALIVE (Socket option, but relevant to TCP connections)
    if (getsockopt(sockfd, SOL_SOCKET, SO_KEEPALIVE, &val, &len) == 0) {
        fprintf(fp, "SO_KEEPALIVE: %d\n", val);
    } else {
        perror("getsockopt SO_KEEPALIVE");
    }

    // SO_RCVBUF
    if (getsockopt(sockfd, SOL_SOCKET, SO_RCVBUF, &val, &len) == 0) {
        fprintf(fp, "SO_RCVBUF: %d\n", val);
    } else {
        perror("getsockopt SO_RCVBUF");
    }

    // SO_SNDBUF
    if (getsockopt(sockfd, SOL_SOCKET, SO_SNDBUF, &val, &len) == 0) {
        fprintf(fp, "SO_SNDBUF: %d\n", val);
    } else {
        perror("getsockopt SO_SNDBUF");
    }

    // Add more TCP options as needed (e.g., TCP_CORK, TCP_INFO, etc.)
}

int main() {
    int sockfd;
    FILE *fp;
    const char *filename = "socket_options.txt";

    // Create a TCP socket
    sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd < 0) {
        perror("socket creation failed");
        return 1;
    }

    // Open the file for writing
    fp = fopen(filename, "w");
    if (fp == NULL) {
        perror("Error opening file");
        close(sockfd);
        return 1;
    }

    // Print IP layer options
    print_ip_options(sockfd, fp);

    // Print TCP layer options
    print_tcp_options(sockfd, fp);

    printf("Socket options written to %s\n", filename);

    // Close the file and socket
    fclose(fp);
    close(sockfd);

    return 0;
}
