https://github.com/enhorse/java-interview/blob/master/db.md#%D0%A7%D1%82%D0%BE-%D1%82%D0%B0%D0%BA%D0%BE%D0%B5-%D1%82%D1%80%D0%B0%D0%BD%D0%B7%D0%B0%D0%BA%D1%86%D0%B8%D1%8F

## Вопрос с собеседования:
Сколько здесь создастся транзакций:
```java
@Service
public class AccountServiceImpl implements AccountService {
    public void updateAccountName(long id, String name) {
    Account account = loadById(id);
    account.setName(name);
    save(account);
    }
    @Transactional(readOnly = true)
    public Account loadById(long id) {
    //.........
    return account;
    }
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public Account save(Account account) {
    //.........
    }
}
@RequiredArgsConstructor
public class AccountView {
private final AccountService accountService;
//................
accountService.updateAccountName(id, name);
//...................
```
Ни одной! Так как когда происходит вызов методов не из другого класса, а из класса для которого надо создать прокси 
и обернуть метод(ы) в транзакции (внутренний вызов), то происходит вызов метода(ов) не прокси класса, а оригинального класса.
Чтобы получить доступ к прокси этого класса, можно, например, получать бины на основе класса через ```BeanFactory```:
```java
@Service
public class AccountServiceImpl implements AccountService {

    private final BeanFactory beanFactory;

    public AccountServiceImpl(BeanFactory beanFactory) {
        this.beanFactory = beanFactory;
    }

//    @Transactional
    public void updateAccountName(long id, String name) {
        Account account = beanFactory.getBean(AccountServiceImpl.class).loadById(id);
        account.setName(name);
        beanFactory.getBean(AccountServiceImpl.class).save(account);
    }
...
``` 
В таком случае для вызываемых методов, над которыми стоит аннотация ```@Transactional``` будет создана новая транзакция, т.е. в данном случае
их создастся 2.  
Если поставить аннотацию ```@Transactional``` над методом ```updateAccountName```, который внутри себя вызывает другие методы через BeanFactory, 
то создастся только одна транзакция для этого метода, в которую будут обернуты транзакции внутри вызываемых через ```BeanFactory``` методов, если 
над ними стоит ```@Transactional``` с ```Propagation``` по умолчанию (```PROPAGATION_REQUIRED```).  
Если же как в примере выше ```Propagation.REQUIRES_NEW```, то будет создана новая внутренняя транзакция, а текущая приостановлена, 
затем внутренняя транзакция зафиксируется (commit), будет возобновлена текущая и в рамках нее выполнится следующий метод, над которым стоит 
```@Transactional``` c дефолтным ```Propagation```. В таком случае будет 2 транзакции, но уже над методами ```updateAccountName``` и ```loadById```. 
Но это только в том случае, если метод вызван у прокси-объекта через ```BeanFactory```! Если происходит внутренний вызов метода, то даже при 
```Propagation.REQUIRES_NEW``` другая транзакция не создастся.   

Для того чтобы не создавать циклическую зависимость от ```BeanFactory```, чтобы не было сильной связанности(coupling) нашего класса со спрингом, можно 
получать прокси-объект и вызывать его методы с помощью интерфейса ```Supplier``` следующим образом:
```java
@Service
public class AccountServiceImpl implements AccountService {

    private final Supplier<AccountServiceImpl> accountServiceSupplier;

    public AccountServiceImpl(BeanFactory beanFactory) {
        this.accountServiceSupplier = () -> beanFactory.getBean(AccountServiceImpl.class);
    }
...
//    @Transactional
    public void updateAccountName(long id, String name) {
        Account account = accountServiceSupplier.get().loadById(id);
        account.setName(name);
...
```
https://www.youtube.com/watch?v=ZVYzVqqVrms&ab_channel=%D0%A3%D0%B3%D0%BE%D0%BB%D0%BE%D0%BA%D1%81%D0%B5%D0%BB%D1%8C%D1%81%D0%BA%D0%BE%D0%B3%D0%BE%D0%B4%D0%B6%D0%B0%D0%B2%D0%B8%D1%81%D1%82%D0%B0  
Там после декларативного управления транзакциями объясняется программное (не смотрел)

---
Если используем финальный класс, то аннотацию ```@Transactional``` следует проставлять над методами интерфейса.
