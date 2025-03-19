# Music-Playlist-Program
//Problem statement: 
Write a program that allows users to create a music playlist consisting of n songs and then display the list of the songs. The program should prompt user to enter the name of each song and add it to the playlist in a sequential order. The playlist should be designed to repeat once it reaches the end of the list.
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX_SONGS 100
typedef struct Song {
    char artist[100];
    char title[100];
    char location[200];
    struct Song *next;
} Song;

Song *songList = NULL;
Song *playlist = NULL;
Song *playlistTail = NULL; // To maintain cyclic property

void addSongToList(char *artist, char *title, char *location) {
    Song *newSong = (Song *)malloc(sizeof(Song));
    strcpy(newSong->artist, artist);
    strcpy(newSong->title, title);
    strcpy(newSong->location, location);
    newSong->next = songList;
    songList = newSong;
}

void addSongToPlaylist(char *title) {
    Song *current = songList;
    while (current) {
        if (strcmp(current->title, title) == 0) {
            Song *newSong = (Song *)malloc(sizeof(Song));
            *newSong = *current;
            newSong->next = NULL;

            if (!playlist) {
                playlist = newSong;
                playlistTail = newSong;
                playlistTail->next = playlist; // Make it cyclic
            } else {
                playlistTail->next = newSong;
                playlistTail = newSong;
                playlistTail->next = playlist; // Maintain cyclic property
            }
            return;
        }
        current = current->next;
    }
    printf("Song not found in song list.\n");
}

void removeSongFromPlaylist(char *title) {
    if (!playlist) return;

    Song *current = playlist, *prev = playlistTail;
    do {
        if (strcmp(current->title, title) == 0) {
            if (current == playlist) {
                playlist = playlist->next;
                playlistTail->next = playlist;
            }
            if (current == playlistTail) {
                playlistTail = prev;
                playlistTail->next = playlist;
            }
            prev->next = current->next;
            free(current);
            return;
        }
        prev = current;
        current = current->next;
    } while (current != playlist);

    printf("Song not found in playlist.\n");
}

void displayPlaylist() {
    if (!playlist) {
        printf("Playlist is empty.\n");
        return;
    }
    Song *current = playlist;
    do {
        printf("%s - %s\n", current->artist, current->title);
        current = current->next;
    } while (current != playlist);
}

void inputSongDetails() {
    char artist[100], title[100], location[200];
    printf("Enter artist name: ");
    fgets(artist, sizeof(artist), stdin);
    artist[strcspn(artist, "\n")] = 0; // Remove newline character

    printf("Enter song title: ");
    fgets(title, sizeof(title), stdin);
    title[strcspn(title, "\n")] = 0; // Remove newline character

    printf("Enter song location: ");
    fgets(location, sizeof(location), stdin);
    location[strcspn(location, "\n")] = 0; // Remove newline character

    addSongToList(artist, title, location);
}

int main() {
    int n;
    printf("Enter the number of songs to add: ");
    scanf("%d", &n);
    getchar(); // Consume the newline character left by scanf

    for (int i = 0; i < n; i++) {
        printf("\nEnter details for song %d:\n", i + 1);
        inputSongDetails();
    }

    char title[100];
    printf("\nEnter the title of the song to add to the playlist: ");
    fgets(title, sizeof(title), stdin);
    title[strcspn(title, "\n")] = 0; // Remove newline character
    addSongToPlaylist(title);

    printf("\nEnter the title of another song to add to the playlist: ");
    fgets(title, sizeof(title), stdin);
    title[strcspn(title, "\n")] = 0; // Remove newline character
    addSongToPlaylist(title);

    printf("\nCurrent Playlist:\n");
    displayPlaylist();

    printf("\nEnter the title of the song to remove: ");
    fgets(title, sizeof(title), stdin);
    title[strcspn(title, "\n")] = 0; // Remove newline character
    removeSongFromPlaylist(title);

    printf("\nUpdated Playlist:\n");
    displayPlaylist();

    return 0;
}
