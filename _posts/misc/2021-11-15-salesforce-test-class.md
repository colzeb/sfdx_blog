## write test then code
Testing is important part of devlopment.
Writing test class first increase your productivity.
by automating your acceptance senario.

### Lets start salesforce test

ˋ@IsTest
public class TestAccountTrigger{

    @IsTest
    public static void testStatus(){
        // lets try to insert a new account
        Account acc = new Account();
        acc.name = 'test Account';
        insert acc;

        // suppose your requirement is so that
        // it will change custom filed renew__c
        // created date + 1 year
        Account actual = [select id, renew__c, createddate from Account where id = :acc.id];
        system.assertEqual(actual.createddate.addYear(1), acutal.renew__c );
    }
}
ˋ
