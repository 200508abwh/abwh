#include <stdio.h>
#include <stdlib.h>

int main()
{
    struct Account {
    char name [50];
    int accountNumber;
    char password[20];
    float money;
    float amount;
    int index;
};
void createAcount(){
    struct Account newAcc;
    FILE *fp;
    printf("Enter your name: ");
    scanf ("%[^\n]" ,newAcc.name);
    printf ("Enter your account number: ");
    scanf ("%d" , &newAcc.accountNumber);
    printf("set a password: ");
    scanf("%s" , newAcc.password);
    newAcc.money = 0.0;
    fp = fopen("my bank.txt" , "a");
    if (fp == NULL) {
            printf("Error opening file!\n");
    return;

    }
    fwrite(&newAcc, sizeof(struct Account), 1,fp);
    printf("Account created successfully!\n");
}
int login(int *index){
    int accNum;
    char pass[20];
    FILE *fp;
    struct Account acc;
    int found = 0, i = 0;
    printf("account Number: ");
    scanf("%d" , &accNum);
    printf("password: ");
    scanf("%s , pass");
    fp = fopen("bank data.txt", "wb");
    if (fp == NULL){
            printf("Error opening file.\n");
    return -1;
    }
    while (fread(&acc, sizeof(struct Account),1,fp)){
            if (acc.accountNumber == accNum && strcmp(acc.password, pass)== 0){
                    found = 1;
            *index = i;
            break;
            }
            i++;
    }
    fclose(fp);
    if (found)
        printf("loing successfully.\n");
    else
        printf("Wrong data.\n");
    return found;
}
void deposimoney(){
    struct Account accs[100];
    FILE *fp;
    int count = 0;
    int i;
    float amount;
    int index;
    if (!login(&index)) return;
    fp = fopen("bankdata.txt" ,"rb");
    if (fp == NULL) {
            printf ("Failed to open file for reading.\n");
    return;
    }

    while (fread(&accs[count], sizeof(struct Account), 1, fp == 1)){
        count++;
    }
    fclose(fp);
    printf ("The amount you want to deposit: ");
    scanf("%f" , &amount);
    printf ("Index: %d\n" , index);
    if (index >= 0 && index < count){
        accs[index].money +=amount;
    } else {

            printf("Invalid index! %d\n" , index);
    return;
    }

    fp = fopen("bankdata.txt" , "wb");
    if (fp == NULL){
            printf ("Failed to open file for writing.\n");
    return;
    }
    for( i = 0;  i < count; i++ ){
        fwrite(&accs[i], sizeof(struct Account), 1, fp);
    }
    fclose(fp);
}
    printf("Deposit successful.\n");
}

   void showBalance(){
       struct Account{
           int accountNumber;
           float money;
       };

       FILE *fp;
       struct Account accs[100];
       int count = 0, index, i;
       if (!login(&index)) return;
       fp = fopen("bankdata.txt", "rb");
       while (fread(&accs[count], sizeof(struct Account), 1, fp))
        count++;
       fclose(fp);
       printf ("2.% :Your current balance f\n",accs[index].money);
   }
void transfermoney() {
    struct Account{
        int AccountNumber;
        int money;
    };
    FILE *fp;
    struct Account accs[100];
    int count = 0, indexfrom = -1 , indexto = -1, i,accNum;
    float amount;
    printf("Enter your account number: ");
    scanf("%d" , &accNum);
    fp = fopen("bankdata.txt" , "rb");
    if (fp == NULL) {
            printf("File not found.\n");
    return;
    }
    while (fread(&accs[count], sizeof(struct Account), 1, fp)){
            if(accs[count].AccountNumber == accNum)
            {
                indexfrom = count;
            }
        count++;
    fclose(fp);
    if (indexfrom == -1){
    printf ("The account does not exist.\n");
    return;
    }
    printf("the account number you want to transfer to: ");
    scanf ("%f" , &amount);
    if (accs[indexfrom].money >= amount){
            accs[indexfrom].money -= amount;
    accs[indexto].money += amount;
    fp = fopen("bankdata.txt", "wb");
    for (i = 0; i < count; i++){
            fwrite(&accs[i],sizeof(struct Account), 1, fp);
    }
    fclose(fp);
    printf("Transfer successful.\n");
    } else {
        printf("Not enough money.\n");

    }
}
void deleteAccount() {
    FILE *fp;
    struct Account accs[100];
    int count = 0, i, index;
    if(!login (&index)) return;
    fp = fopen("bankdata.txt" , "rb");
    while (fread(&accs[count],sizeof(struct Account), 1, fp))
        count ++;
    fclose(fp);
    if (accs[index].money != 0) {
printf ("The account cannot be deleted. The balance must be 0.\n");
return;
    }
    fp = fopen("bankdata.txt" , "wb");
    for (i = 0; i < count; i++){
            if (i != index)
            fwrite(&accs[i], sizeof(struct Account), 1, fp);
    }
    fclose(fp);
    printf("Account Deleted.\n");
}

 int main() {
     int choice ;
     while (1) {
            printf ("\n*** Bank Management System ***\n");
     printf ("1. Create a new account\n");
     printf ("2.Log in\n");
     printf ("3. Deposit an amount\n");
     printf ("4. View Balance\n");
     printf ("5. Transfer an amount\n");
     printf ("6.Delete account\n");
     printf ("7.Exit\n");
     printf ("Enter your choice: ");
     scanf ("%d" , &choice);
     switch (choice){
         case 1:createAccount();
         break;
         case 2:{
             int i;
             login(&i);
             break ;
         }
         case 3: depositmoney(); break;
         case 4: showbalance(); break;
         case 5: transfermoney(); break;
         case 6: deleteAccount (); break;
         case 7: exit(0);  break;}
             printf("Invalid option.\n");
          break;
          }

        }

       return 0;
       }

