
#include<stdio.h>
#include<string.h>
struct books{
	char title[50];
	char author[50];
};
// functions
void add_book();
void display_book();
void display_menu();
int search_book(char name[]);
void borrow_book(char name[]);
void return_book(char name[]);

int main()
{

	char name[50],ch[1];
	int choice;
	
	begin://start again after a function
	//display the choice menu and read the choice
	display_menu();
	scanf("%d",&choice);
	
	switch(choice)
	{
		case 1:
			add_book();
			break;
			
		case 2:
			display_book();
			break;
			
		case 3:
			printf("Please Enter the name of the book you are searching for: ");
			scanf("%s",name);
			if(search_book(name))//the function return either a 1 or a 0
			{
				printf("The book %s is in the library. Would you like to borroww it?",name);
				scanf("%s",ch);
				if(ch[0]=='y'||ch[0]=='Y')
				{
					borrow_book(name);
				}
			}
			else
			{
				printf("The book %s is not available\n",name);
			}
			break;
			
		case 4:
			printf("Enter the name of the book you would like to borrow: ");
			scanf("%s",name);
			if(search_book(name))//if book is available then borrow the book
			{
				borrow_book(name);
			}
			else
			{
				printf("The book you are looking for is not available\n");
			}
			break;
				
		case 5:
			printf("Please enter the name of the book you are returning\n");
			scanf("%s",name);
			return_book(name);		
			break;
		case 0:
			printf("Thank you for using the program.");
			return 0;
		default :
			printf("Invalid choice\n");
	}
	goto begin;//unless choice is 0 start again
}

void add_book()
{
	struct books book;
	char ch[1];
	start:
	FILE*fptr=fopen("C:/Users/ACER/Desktop/books.txt","a");
	printf("\nEnter the name of the book: ");
	scanf("%s",book.title);
	printf("\nEnter the name of the author: ");
	scanf("%s",book.author);
	fprintf(fptr,"%s\t%s\n",book.title,book.author);
	fclose(fptr);
	FILE*fp=fopen("availablebooks.txt","a");
	//save only name of the book in a seperate file to make searching easy later
	fprintf(fp,"%s\n",book.title);
	fclose(fp);
	printf("\nDo you want to add another book?(y/n)");
	scanf("%s",ch);
	if(ch[0]=='y'||ch[0]=='Y')
	{
		goto start;//if choice is y or Y add another book
	}
}

void display_book()
{
	int sn=1;
	struct books book;
	FILE*fptr;	
	fptr=fopen("books.txt","r");
	printf("S.N.\tTitle\t\tAuthor\n");
	while(fscanf(fptr,"%s%s",book.title,book.author)!=EOF)
	{
		printf("%d\t",sn);
		printf("%s\t\t",book.title);
		printf("%s\n",book.author);
		sn++;//for the serial number of the list
	}
	fclose(fptr);
}

void display_menu()
{
	printf("\nMenu\n");
	printf("1:Add book to the library.\n");
	printf("2:Display all books registered in the library.\n");
	printf("3:Search available books.\n");
	printf("4:Borrow books.\n");
	printf("5:Retrun books.\n");
	printf("0:Exit the program.\n");
	printf("Enter your choice: ");
}

int search_book(char name[])
{
	char bookname[50];
	int found=0;
	FILE*fptr=fopen("availablebooks.txt","r");
	while(fscanf(fptr,"%s",bookname)!=EOF)
	{
		if(strcmp(name,bookname)==0)
		{
			//if name = bookname, then found becomes 1 or true else it remains 0 or false
			found=1;
			break;
		}
	}
	fclose(fptr);
	return found;
}

void borrow_book(char name[])
{
	//removing the name of the book to be borrowed from the available books file
	int i=0,j;
	char bookname[50];
	char book[50][50];//array of array of characters to store multiple names of the book
	FILE*fptr=fopen("availablebooks.txt","r");
	while(fscanf(fptr,"%s",bookname)!=EOF)
	{
		if(strcmp(name,bookname)==0)
		{
			continue;
		}
		//copy names of the book to array except if name = bookname ie borrowed book
		strcpy(book[i],bookname);
		i++;//for the array
	}
	fclose(fptr);
	fptr=fopen("availablebooks.txt","w");//opening in write mode to erase the previous data
	for(j=0;j<i;j++)//run for the same time as the number of elemets in the array
	{
		fprintf(fptr,"%s\n",book[j]);
	}
	fclose(fptr);
	printf("The book has been borrowed.\n");
}

void return_book(char name[])
{
	//just add the name of the returned book to available books
	FILE*fp=fopen("availablebooks.txt","a");
	fprintf(fp,"%s\n",name);
	fclose(fp);
	printf("The book has been returned.\n");
}
