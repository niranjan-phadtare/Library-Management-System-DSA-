#include<stdio.h>
#include<stdlib.h>
#include<string.h>

typedef int bool;
#define true 1
#define false 0

struct node{
  struct node *prev;
  char title[20];
  char author[20];
  int id;
  struct node *next;
};

bool InsertLast(struct node ** ,struct node ** ,char*,char*,int);
void Display(struct node* , struct node*);
void freeList(struct node*);
int search(struct node* , struct node*,char*);
bool DeleteAtPos(struct node ** , struct node ** ,int);
bool DeleteFirst(struct node**,struct node**);
bool DeleteLast(struct node**,struct node**);
int count(struct node *, struct node *);
void saveToFile(struct node *);
void loadFromFile(struct node **, struct node **);
void updateFile(struct node *first);

int main(){
    struct node *head = NULL;
    struct node *tail = NULL;
 
    char title[50]={'\0'};
    char name[30]={'\0'};
    int id = 0;
    int n = 0;
    int choice = 0;
    /* 
    printf("\n\t\t\tLibrary Management System\t\t\t\n\n");
    printf(" 1 : Insert Book ");
    printf(" 2 : search Book");
    printf(" 3 : Exit ");
    scanf("%d",&choice);
*/
  int f = 0;
  printf("If you want to Insert Book in Library Enter 1 -  ");
  scanf("%d",&f);
  if(f==1){   
        printf("\nEnter the no. of books you want to store in Library ");
        scanf("%d",&n);
        
        for(int i = 0 ; i < n ; i++){
            printf("Enter title : ");
            scanf(" %[^\n]",title);
            printf("Enter author's name : ");
            scanf("  %[^\n]",name); 
            printf("Enter the ID of Book : ");
            scanf(" %d",&id);
            InsertLast(&head, &tail, title, name, id);
        }
        saveToFile(head);
    }
    else{
        return -1;
    }
    Display(head,tail);
    printf("Do you want delete any book ? then enter 1  - ");
    scanf(" %d",&f);
    int p = 0;
    scanf(" %d",&p);
    if(f==1){
      printf("At what position you want to delete the book ? : ");
      DeleteAtPos(&head,&tail,p);
      updateFile(head);
      printf("After removing book from library ");
    }
    else if(f==0){
        printf("Nothing is removed");
    }
   
    char check[50]= {'\0'};
    printf("\nEnter the title to check it is present in Library or not : ");
    scanf(" %[^\n]",check);
   if(search(head,tail,check)==0){
    printf(" %s is present in the Library",check);
   }
   else{
    printf(" %s is not present in the Library",check);
   }
    Display(head,tail);
    freeList(head);
    return 0;
}


void saveToFile(struct node *first) {
    FILE *file = fopen("library.txt", "w");
    if (file == NULL) {
        printf("Error opening file for writing!\n");
        return;
    }

    struct node *temp = first;
    if (first == NULL) {
        fclose(file);
        return;
    }

    do {
        fprintf(file, "%d,%s,%s\n", temp->id, temp->title, temp->author);
        temp = temp->next;
    } while (temp != first);

    fclose(file);
}

// Function to load books from file


// Function to update file after deletion
void updateFile(struct node *first) {
    remove("library.txt"); // Remove old file
    saveToFile(first); // Save updated list to file
}
int count(struct node *first, struct node *last){
    int i = 1;
    if((first!=NULL)&&(last!=NULL)){
        while(first!=last){
          first = first->next;
          i++;
        }
    }   
    return i;
}
bool DeleteFirst(struct node **first , struct node **last ){
    if((*first)==NULL && (*last)==NULL){
       return false;
    }
    else{
       *first = (*first)->next;
       (*first)->prev = *last;
       (*last)->next = *first;
    }
    return true;
 }
 
 bool DeleteLast(struct node **first , struct node **last){
    if((*first)==NULL && (*last)==NULL){
         return false;
    }
    else{
       (*last) = (*last)->prev;
       (*last)->next = *first;
       (*first)->prev = *last;
      }
      return true;
 }
bool InsertLast(struct node **first, struct node **last, char *t, char *n, int no) {
    struct node *newnode = (struct node *)malloc(sizeof(struct node));
    if (!newnode) return false; 

    newnode->next = NULL;
    newnode->prev = NULL;

    
    strcpy(newnode->title, t);
    strcpy(newnode->author, n);
    newnode->id = no;

    if ((*first) == NULL && (*last) == NULL) {
        *first = *last = newnode;
        (*first)->next = (*first)->prev = newnode;
    } else {
        (*last)->next = newnode;
        newnode->prev = *last;
        newnode->next = *first;
        *last = newnode;
    }

    return true;
}

bool DeleteAtPos(struct node **first , struct node **last , int pos){
    if((*first)==NULL && (*last)==NULL){
       return false;
     }
     else if(pos<=0){
       return false;
     }
     else if(pos>count(*first,*last)+1){
       return false;
     }
     else if(pos==1){
       DeleteFirst(first,last);
     }
     else if(pos>=count(*first,*last)){
       DeleteLast(first,last);
     }
     else{
       struct node *temperory1 = *first;
       struct node *temperory2 = NULL;
       while(pos-1>1){
         temperory1 = temperory1->next;
         pos--;
       }
       temperory2 = temperory1;
       temperory1->next->next->prev = temperory2;
       temperory1->next = temperory1->next->next;
     }
     return true;
 }
 


void Display(struct node *first,struct node *last){
    
    if((first!=NULL)&&(last!=NULL)){ 
   do{
      printf("\n%d\t%s\t%s\t\n",(first)->id,(first)->title,(first)->author);
      first = (first)->next;
    } while(first != last->next);
 }
   
}

void freeList(struct node *first) {
    struct node *temp;
    struct node *start =first;
    if (first == NULL){
        return;
    } 

    do {
        temp = first;
       first = first->next;
        free(temp);
    } while (first != start);
}
int search(struct node *first, struct node *last, char *t) {
    if (first == NULL) {
        return -1; 
    }

    struct node *temp = first;
    do {
        if (strcmp(temp->title, t) == 0) { 
            return 0; 
        }
        temp = temp->next;
    } while (temp != last->next); 

    return -1; 
}

