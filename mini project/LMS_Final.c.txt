#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_Books 200
#define FILE_1 "D:\\MIT\\C\\books.csv.txt"
#define FILE_2 "D:\\MIT\\C\\Students.txt"
#define FILE_3 "D:\\MIT\\C\\Issued_Books.txt"
#define Max_Stud 50
#define Max_Issue 100

struct Book
{
    int id;
    char title[100];
    char author[100];
    char genre[50];
    int year;
    int totalCopies;
    int available;
};

struct Students
{
    int prn;
    char name[100];
    char pwd[15];
};

struct Issued_Books
{
    int issue_id;
    char title[100];
    char issue_date[15];
    char due_date[15];
    char return_date[15];
    int prn;

    char return_status[20];
    float fine;
};
struct Book books[MAX_Books];
int book_count = 0;
struct Students students[Max_Stud];
int student_count = 0;
struct Issued_Books issue[Max_Issue];
int issue_count = 0;
void calculateDueDate(char issue_date[], char due_date[]);
void loadFromBooks()
{
    FILE *fp = fopen(FILE_1, "r");

    if (fp == NULL)
    {
        fp = fopen(FILE_1, "w");
        fprintf(fp, "ID,Title,Author,Genre,Year,TotalCopies,Available\n");
        fclose(fp);
        return;
    }

    char line[300];
    book_count = 0;

    fgets(line, sizeof(line), fp);

    while (fgets(line, sizeof(line), fp))
    {
        int len = strlen(line);
        while (len > 0 && (line[len - 1] == '\n' || line[len - 1] == '\r'))
        {
            line[len - 1] = '\0';
            len--;
        }

        if (sscanf(line, "%d,%99[^,],%99[^,],%49[^,],%d,%d,%d",
                   &books[book_count].id,
                   books[book_count].title,
                   books[book_count].author,
                   books[book_count].genre,
                   &books[book_count].year,
                   &books[book_count].totalCopies,
                   &books[book_count].available) == 7)
        {
            book_count++;
        }
    }

    fclose(fp);
}

void loadStudents()
{
    FILE *fp = fopen(FILE_2, "r");
    if (fp == NULL)
    {
        printf("Error opening student file!\n");
        return;
    }

    char line[200];

    if (fgets(line, sizeof(line), fp) == NULL)
    {
        fclose(fp);
        return;
    }

    student_count = 0;

    while (fgets(line, sizeof(line), fp) && student_count < Max_Stud)
    {
        int len = strlen(line);
        while (len > 0 && (line[len - 1] == '\n' || line[len - 1] == '\r'))
        {
            line[len - 1] = '\0';
            len--;
        }

        int prn;
        char name[100], pwd[15];

        if (sscanf(line, "%d,%99[^,],%14[^\n]", &prn, name, pwd) == 3)
        {
            int pwlen = strlen(pwd);
            if (pwlen > 0 && pwd[pwlen - 1] == '\r')
                pwd[pwlen - 1] = '\0';

            students[student_count].prn = prn;
            strcpy(students[student_count].name, name);
            strcpy(students[student_count].pwd, pwd);
            student_count++;
        }
    }

    fclose(fp);
}

void loadIssuedBooks()
{
    FILE *fp = fopen(FILE_3, "r");

    if (fp == NULL)
    {
        fp = fopen(FILE_3, "w");
fprintf(fp, "IssueID,Title,IssueDate,DueDate,ReturnDate,PRN,ReturnStatus,Fine\n");
        fclose(fp);
        return;
    }

    char line[300];
    issue_count = 0;

    fgets(line, sizeof(line), fp);

    while (fgets(line, sizeof(line), fp) && issue_count < Max_Issue)
    {
        int len = strlen(line);
        while (len > 0 && (line[len - 1] == '\n' || line[len - 1] == '\r'))
        {
            line[len - 1] = '\0';
            len--;
        }
if (sscanf(line,
    "%d,%99[^,],%14[^,],%14[^,],%14[^,],%d,%19[^,],%f",
    &issue[issue_count].issue_id,
    issue[issue_count].title,
    issue[issue_count].issue_date,
    issue[issue_count].due_date,
    issue[issue_count].return_date,
    &issue[issue_count].prn,
    issue[issue_count].return_status,
    &issue[issue_count].fine) == 8)
{
    issue_count++;
}
    }

    fclose(fp);
}

void saveToBooks()
{
    FILE *fp = fopen(FILE_1, "w");

    fprintf(fp, "ID,Title,Author,Genre,Year,TotalCopies,Available\n");

    for (int i = 0; i < book_count; i++)
    {
        fprintf(fp, "%d,%s,%s,%s,%d,%d,%d\n",
                books[i].id,
                books[i].title,
                books[i].author,
                books[i].genre,
                books[i].year,
                books[i].totalCopies,
                books[i].available);
    }

    fclose(fp);
}
void saveStudents()
{
    FILE *fp = fopen(FILE_2, "w");

    fprintf(fp, "PRN,Name,Password\n");

    for(int i = 0; i < student_count; i++)
    {
        fprintf(fp, "%d,%s,%s\n",
                students[i].prn,
                students[i].name,
                students[i].pwd);
    }

    fclose(fp);
}
void signupStudent()
{
    loadStudents();

    if(student_count >= Max_Stud)
    {
        printf("Student limit reached.\n");
        return;
    }

    int prn;
    char name[100], pwd[15];

    printf("Enter PRN: ");
    scanf("%d", &prn);
    getchar();


    for(int i = 0; i < student_count; i++)
    {
        if(students[i].prn == prn)
        {
            printf("PRN already exists.\n");
            return;
        }
    }

    printf("Enter Name: ");
    fgets(name, sizeof(name), stdin);
    name[strcspn(name, "\n")] = 0;

    printf("Enter Password: ");
    fgets(pwd, sizeof(pwd), stdin);
    pwd[strcspn(pwd, "\n")] = 0;

    students[student_count].prn = prn;
    strcpy(students[student_count].name, name);
    strcpy(students[student_count].pwd, pwd);

    student_count++;

    saveStudents();

    printf("Signup successful!\n");
}
void saveIssuedBooks()
{
    FILE *fp = fopen(FILE_3, "w");

   fprintf(fp, "IssueID,Title,IssueDate,DueDate,ReturnDate,PRN,ReturnStatus,Fine\n");
    for (int i = 0; i < issue_count; i++)
    {
        fprintf(fp, "%d,%s,%s,%s,%s,%d,%s,%.2f\n",
        issue[i].issue_id,
        issue[i].title,
        issue[i].issue_date,
        issue[i].due_date,
        issue[i].return_date,
        issue[i].prn,
        issue[i].return_status,
        issue[i].fine);
    }

    fclose(fp);
}

void addBook()
{
    if (book_count >= MAX_Books)
    {
        printf("Library is full!\n");
        return;
    }

    struct Book b;

    if (book_count == 0)
        b.id = 1;
    else
        b.id = books[book_count - 1].id + 1;

    getchar();

    printf("Enter Title: ");
    fgets(b.title, sizeof(b.title), stdin);
    b.title[strcspn(b.title, "\n")] = 0;

    printf("Enter Author: ");
    fgets(b.author, sizeof(b.author), stdin);
    b.author[strcspn(b.author, "\n")] = 0;

    printf("Enter Genre: ");
    fgets(b.genre, sizeof(b.genre), stdin);
    b.genre[strcspn(b.genre, "\n")] = 0;

    printf("Enter Year: ");
    scanf("%d", &b.year);

    printf("Enter Total Copies: ");
    scanf("%d", &b.totalCopies);

    b.available = b.totalCopies;

    books[book_count++] = b;

    saveToBooks();

    printf("Book added successfully! ID generated: %d\n", b.id);
}

void displayBooks()
{
    if (book_count == 0)
    {
        printf("No books available.\n");
        return;
    }

    printf("\n--- Book List ---\n");

    for (int i = 0; i < book_count; i++)
    {
        printf("ID: %d\n", books[i].id);
        printf("Title: %s\n", books[i].title);
        printf("Author: %s\n", books[i].author);
        printf("Genre: %s\n", books[i].genre);
        printf("Year: %d\n", books[i].year);
        printf("Available: %d/%d\n\n", books[i].available, books[i].totalCopies);
    }
}

void searchBook()
{
    char title[100];
    printf("Enter Book title to search: ");
    getchar();
    fgets(title, sizeof(title), stdin);
    title[strcspn(title, "\n")] = 0;

    for (int i = 0; i < book_count; i++)
    {
        if (strcmp(books[i].title, title) == 0)
        {
            printf("Book Found!\n");
            printf("Book ID: %d\n", books[i].id);
            printf("Author: %s\n", books[i].author);
            printf("Genre: %s\n", books[i].genre);
            printf("Year: %d\n", books[i].year);
            printf("Available Copies: %d/%d\n", books[i].available, books[i].totalCopies);
            return;
        }
    }

    printf("Book not found.\n");
}

void issueBook()
{
    loadFromBooks();
    loadIssuedBooks();

    char title[100];
    int prn;

    printf("Enter Student PRN: ");
    scanf("%d", &prn);
    getchar();

    printf("Enter Book Title to issue: ");
    fgets(title, sizeof(title), stdin);
    title[strcspn(title, "\n")] = 0;

    for (int i = 0; i < book_count; i++)
    {
        if (strcmp(books[i].title, title) == 0)
        {
            if (books[i].available > 0)
            {
                books[i].available--;
                saveToBooks();

                char issue_date[15];
                char due_date[15];

                printf("Enter issue date (DD-MM-YYYY): ");
                fgets(issue_date, sizeof(issue_date), stdin);
                issue_date[strcspn(issue_date, "\n")] = 0;


                calculateDueDate(issue_date, due_date);

                if (issue_count < Max_Issue)
                {
                    issue[issue_count].issue_id = issue_count + 1;
                    strcpy(issue[issue_count].title, title);
                    strcpy(issue[issue_count].issue_date, issue_date);
                    strcpy(issue[issue_count].due_date, due_date);
                    strcpy(issue[issue_count].return_date, "N/A");
                    issue[issue_count].prn = prn;
                    strcpy(issue[issue_count].return_status, "Not Returned");
                    issue[issue_count].fine = 0;
                    issue_count++;
                    saveIssuedBooks();
                }

                printf("Book issued successfully!\n");
                printf("Due Date: %s\nFailing to return the book on or before this date will lead to a fine of Rs.1 per day delay\n", due_date);              }
            else
            {
                printf("No copies available.\n");
            }
            return;
        }
    }

    printf("Book not found.\n");
}
void calculateDueDate(char issue_date[], char due_date[])
{
    int d, m, y;

    sscanf(issue_date, "%d-%d-%d", &d, &m, &y);

    d += 14;
    if(m==4||m==6||m==9||m==11)
    {
    if(d > 30)
    {
        d -= 30;
        m++;
    }
    }
    else
    {
        if(d>31)
        {
            d=d-31;
            m++;
        }
    }
    if(m > 12)
    {
        m = 1;
        y++;
    }

    sprintf(due_date, "%02d-%02d-%04d", d, m, y);
}
void checkOverdue()
{
    loadIssuedBooks();

    char today[15];
    printf("Enter today's date (DD-MM-YYYY): ");
    getchar();
    fgets(today, sizeof(today), stdin);
    today[strcspn(today, "\n")] = 0;

    int d2, m2, y2;
    sscanf(today, "%d-%d-%d", &d2, &m2, &y2);
    int today_total = y2*365 + m2*30 + d2;

    for(int i = 0; i < issue_count; i++)
    {
        if(strcmp(issue[i].return_status, "Not Returned") == 0)
        {
            int d1, m1, y1;
            sscanf(issue[i].due_date, "%d-%d-%d", &d1, &m1, &y1);
            int due_total = y1*365 + m1*30 + d1;

            if(today_total > due_total)
            {
                int late_days = today_total - due_total;
float fine = late_days * 1;
                printf("\nOverdue Student PRN: %d\n", issue[i].prn);
                printf("Book: %s\n", issue[i].title);
                printf("Late Days: %d\n", late_days);
                printf("Fine: ₹%.2f\n", fine);
            }
        }
    }
}

void returnBook()
{
    loadFromBooks();
    loadIssuedBooks();

    char title[100];
    int prn;

    printf("Enter Student PRN: ");
    scanf("%d", &prn);
    getchar();

    printf("Enter Book title to return: ");
    fgets(title, sizeof(title), stdin);
    title[strcspn(title, "\n")] = 0;

    for (int i = 0; i < issue_count; i++)
    {
        if (strcmp(issue[i].title, title) == 0 &&
            issue[i].prn == prn &&
            strcmp(issue[i].return_status, "Not Returned") == 0)
        {
            for (int j = 0; j < book_count; j++)
            {
                if (strcmp(books[j].title, title) == 0)
                {
                    if (books[j].available < books[j].totalCopies)
                    {
                        books[j].available++;
                        saveToBooks();
                    }
                    break;
                }
            }

            strcpy(issue[i].return_status, "Returned");

            char return_date[15];
            printf("Enter return date (DD-MM-YYYY): ");
            fgets(return_date, sizeof(return_date), stdin);
            return_date[strcspn(return_date, "\n")] = 0;
            strcpy(issue[i].return_date, return_date);
            int d1, m1, y1;
            int d2, m2, y2;
            sscanf(issue[i].due_date, "%d-%d-%d", &d1, &m1, &y1);
            sscanf(return_date, "%d-%d-%d", &d2, &m2, &y2);
            int due_total = y1*365 + m1*30 + d1;
            int return_total = y2*365 + m2*30 + d2;

            if(return_total > due_total)
            {
              int late_days = return_total - due_total;
              issue[i].fine = late_days * 1;
              printf("Late by %d days\n", late_days);
              printf("Fine: Rs.%.2f\n", issue[i].fine);
            }
            else
            {
              issue[i].fine = 0;
              printf("Returned on time. No fine.\n");
            }
            saveIssuedBooks();
            printf("Book returned successfully.\n");
            return;
        }
    }
printf("No matching issued record found for this PRN and book title.\n");
}

void countBooks()
{
    printf("Total books in library: %d\n", book_count);
}

int main()
{
    loadFromBooks();

    int choice, subChoice;
    char username[50], password[50];
    int prn;

    while (1)
    {
        printf("\n1.Sign in as Admin\n2.Sign in as User\n3.Sign up as User\n0.Exit\n");
        scanf("%d", &choice);

        if (choice == 1)
        {
            printf("Username: ");
            scanf("%s", username);

            printf("Password: ");
            scanf("%s", password);

            if (strcmp(username, "FOP") == 0 &&
                strcmp(password, "MitWpu123") == 0)
            {
                do
                {
                    printf("\n1.Add\n2.Display\n3.Search\n4.Issue\n5.Return\n6.Count\n0.Logout\n");
                    scanf("%d", &subChoice);

                    switch (subChoice)
                    {
                        case 1: addBook();     break;
                        case 2: displayBooks(); break;
                        case 3: searchBook();   break;
                        case 4: issueBook();    break;
                        case 5: returnBook();   break;
                        case 6: countBooks();   break;
                        case 0: printf("Logging out...\n"); break;
                        default: printf("Invalid choice\n");
                    }
                } while (subChoice != 0);
            }
            else
            {
                printf("Invalid credentials!\n");
            }
        }
        else if (choice == 2)
        {
            loadStudents();

            int found = 0;

            printf("Enter your PRN: ");
            scanf("%d", &prn);
            getchar();

            printf("Password: ");
            fgets(password, sizeof(password), stdin);

            int len = strlen(password);
            while (len > 0 && (password[len - 1] == '\n' || password[len - 1] == '\r'))
            {
                password[len - 1] = '\0';
                len--;
            }

            for (int i = 0; i < student_count; i++)
            {
                if (strcmp(students[i].pwd, password) == 0 && students[i].prn == prn)
                {
                    found = 1;

                    do
                    {
                        printf("\n1.Display\n2.Search\n0.Logout\n");
                        scanf("%d", &subChoice);

                        switch (subChoice)
                        {
                            case 1: displayBooks(); break;
                            case 2: searchBook();   break;
                            case 0: printf("Logging out...\n"); break;
                            default: printf("Invalid choice\n");
                        }
                    } while (subChoice != 0);

                    break;
                }
            }

            if (!found)
            {
                printf("Invalid login credentials\n");
            }
        }
        else if(choice==3)
        {
            signupStudent();
            saveStudents();
        }
        else if (choice == 0)
        {
            printf("Exiting program...\n");
            break;
        }
        else
        {
            printf("Invalid choice\n");
        }
    }

    return 0;
}

