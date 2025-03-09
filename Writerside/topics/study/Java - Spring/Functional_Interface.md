# Functional Interface

#### Predicate
T->boolean : boolean test(T t)
#### Consumer
T → void : void accept(T t)
#### Supplier
() → T : T get()
#### Function<T,R> T→R
R apply(T t)
#### Comparator (T,T) → int 
int compare(T o1, T o2)
#### Runnable ()→void
void run
#### Callable () →T
V call()
