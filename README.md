#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define FILENAME "bankdata.txt"

struct Account {
    char name[50];
    int accountNumber;
    char password[20];
    float money;
};

// createAccount
void createAccount() {
    struct Account newAcc;
    FILE *fp = fopen(FILENAME, "ab");
    if (fp == NULL) {
        printf("Error opening file!\n");
        return;
    }

    getchar(); // لتفادي مشاكل الإدخال
    printf("Enter your name: ");
    fgets(newAcc.name, sizeof(newAcc.name), stdin);
    newAcc.name[strcspn(newAcc.name, "\n")] = '\0'; // إزالة السطر الجديد

    printf("Enter your account number: ");
    scanf("%d", &newAcc.accountNumber);

    printf("Set a password: ");
    scanf("%s", newAcc.password);

    newAcc.money = 0.0;

    fwrite(&newAcc, sizeof(struct Account), 1, fp);
    fclose(fp);

    printf("Account created successfully!\n");
}

// login
int login(int *index) {
    int accNum, i = 0;
    char pass[20];
    struct Account acc;
    FILE *fp = fopen(FILENAME, "rb");
    if (fp == NULL) {
        printf("File not found.\n");
        return 0;
    }

    printf("Account Number: ");
    scanf("%d", &accNum);
    printf("Password: ");
    scanf("%s", pass);

    while (fread(&acc, sizeof(struct Account), 1, fp)) {
        if (acc.accountNumber == accNum && strcmp(acc.password, pass) == 0) {
            *index = i;
            fclose(fp);
            printf("Login successful.\n");
            return 1;
        }
        i++;
    }

    fclose(fp);
    printf("Incorrect account number or password.\n");
    return 0;
}

// depositMoney
void depositMoney() {
    struct Account accs[100];
    int count = 0, index;
    float amount;
    FILE *fp = fopen(FILENAME, "rb");
    if (fp == NULL) {
        printf("Failed to open file.\n");
        return;
    }

    while (fread(&accs[count], sizeof(struct Account), 1, fp))
        count++;
    fclose(fp);

    if (!login(&index)) return;

    printf("Enter the amount to deposit: ");
    scanf("%f", &amount);

    if (index >= 0 && index < count) {
        accs[index].money += amount;
    } else {
        printf("Invalid account index.\n");
        return;
    }

    fp = fopen(FILENAME, "wb");
    for (int i = 0; i < count; i++) {
        fwrite(&accs[i], sizeof(struct Account), 1, fp);
    }
    fclose(fp);

    printf("Deposit successful.\n");
}
// showBalance
void showBalance() {
    struct Account accs[100];
    int count = 0, index;
    FILE *fp = fopen(FILENAME, "rb");
    if (fp == NULL) {
        printf("File not found.\n");
        return;
    }

    while (fread(&accs[count], sizeof(struct Account), 1, fp))
        count++;
    fclose(fp);

    if (!login(&index)) return;

    if (index >= 0 && index < count) {
        printf("Your current balance is: %.2f\n", accs[index].money);
    } else {
        printf("Invalid index.\n");
    }
}

// transferMoney
void transferMoney() {
    struct Account accs[100];
    int count = 0, fromIndex = -1, toIndex = -1, targetAcc;
    float amount;

    FILE *fp = fopen(FILENAME, "rb");
    if (fp == NULL) {
        printf("File not found.\n");
        return;
    }

    while (fread(&accs[count], sizeof(struct Account), 1, fp))
        count++;
    fclose(fp);

    if (!login(&fromIndex)) return;

    printf("Enter recipient's account number: ");
    scanf("%d", &targetAcc);
    for (int i = 0; i < count; i++) {
        if (accs[i].accountNumber == targetAcc) {
            toIndex = i;
            break;
        }
    }

    if (toIndex == -1) {
        printf("Target account not found.\n");
        return;
    }

    printf("Enter amount to transfer: ");
    scanf("%f", &amount);

    if (accs[fromIndex].money >= amount) {
        accs[fromIndex].money -= amount;
        accs[toIndex].money += amount;

        fp = fopen(FILENAME, "wb");
        for (int i = 0; i < count; i++) {
            fwrite(&accs[i], sizeof(struct Account), 1, fp);
        }
        fclose(fp);
        printf("Transfer successful.\n");
    } else {
        printf("Insufficient balance.\n");
    }
}

// deleteAccount
void deleteAccount() {
    struct Account accs[100];
    int count = 0, index;
    FILE *fp = fopen(FILENAME, "rb");
    if (fp == NULL) {
        printf("File not found.\n");
        return;
    }

    while (fread(&accs[count], sizeof(struct Account), 1, fp))
        count++;
    fclose(fp);

    if (!login(&index)) return;

    if (accs[index].money != 0) {
        printf("Cannot delete account with non-zero balance.\n");
        return;
    }

    fp = fopen(FILENAME, "wb");
    for (int i = 0; i < count; i++) {
        if (i != index)
            fwrite(&accs[i], sizeof(struct Account), 1, fp);
    }
    fclose(fp);

    printf("Account deleted successfully.\n");
}

//main
int main() {
    int choice;
    while (1) {
        printf("\n*** Bank Management System ***\n");
        printf("1. Create a new account\n");
        printf("2. Deposit money\n");
        printf("3. View balance\n");
        printf("4. Transfer money\n");
        printf("5. Delete account\n");
        printf("6. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: createAccount(); break;
            case 2: depositMoney(); break;
            case 3: showBalance(); break;
            case 4: transferMoney(); break;
            case 5: deleteAccount(); break;
            case 6: exit(0);
            default: printf("Invalid option. Try again.\n");
        }
    }
    return 0;
}
