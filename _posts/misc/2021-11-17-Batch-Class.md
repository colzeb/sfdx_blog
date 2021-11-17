Batch class are asynchronuns Exceution.
## Problem
Lets assume we have 3 classes A, B, C each with master detail with contact.
- A have 10000Record with parent as contact Aid
- B have 20000Record with parent as contact Aid
- C have 10000Record with parent as contact Aid

now move all this record to contact Bid


now you can implement batch class

## start
it gives any iterable or database queryLocator(preferred)
[databse query preferred](https://developer.salesforce.com/forums/?id=906F0000000B3TIIA0)
[query locator class doc](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex_methods_system_database_batch.htm#apex_methods_system_database_batch)
it only run one time when batch start

## execute
now execute method run when there is free cpu time 
in a batch of 200(can be changed on calling batch)


## finish
this will run only one time when batch job will finish
typically for job finish notification


```
public class TaskBatch implements Database.Batchable<sObject> {

    public Database.QueryLocator start(Database.BatchableContext BC) {
        // this is bad it will have 50k record limit
        // and 6mb of stack
        // use database.querylocator
        // return [select id, (select id, aContact__c from a__r), (select id, bContact__c from b__r), (select id, cContact__c from c__r) from contact];
        // this is also bad practice as it is using subquery in start method, try use subquery in execute method
        String soql = 'select id, (select id, aContact__c from a__r), (select id, bContact__c from b__r), (select id, cContact__c from c__r) from contact';
        return Database.getQueryLocator(soql);
    }

    
    
    public void execute(Database.BatchableContext bc, List<Contact> contactList){
        for(contact con : contactList){
            for(A__c aobj : con.a__r){
                // do your fancy logic
                aobj.
            }
            for(B__c bobj : con.b__r){
                
            }
            for(C__c cobj : con.c__r){
                
            }
        }
    }
    
    public  void finish(Database.BatchableContext bc){
        
    }

}
```

## run batch job


### can you write batch class in public 
[batch public vs global](https://developer.salesforce.com/forums/?id=9060G0000005QpfQAE)

- to use a callout in batch Apex, specify Database.AllowsCallouts in the class definition


- Use Database.Stateful with the class definition if you want to share instance member variables or data across job transactions. Otherwise, all member variables are reset to their initial state at the start of each transaction.
- Methods declared as future aren’t allowed in classes that implement the Database.Batchable interface.
- Methods declared as future can’t be called from a batch Apex class.

- You can chain a batch job by calling Database.executeBatch or System.scheduleBatch from the finish method of the current batch class. The new batch job will start after the current batch job finishes.

- Batch Apex jobs run faster when the start method returns a QueryLocator object that doesn't include related records via a subquery. Avoiding relationship subqueries in a QueryLocator allows batch jobs to run using a faster, chunked implementation. If the start method returns an iterable or a QueryLocator object with a relationship subquery, the batch job uses a slower, non-chunking, implementation (slow). 

[official doc ](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch_interface.htm)