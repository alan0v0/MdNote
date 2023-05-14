projectreactor 扩展了reactivestreams Subscriber接口，要求subscriber具有context功能。

```java
public interface CoreSubscriber<T> extends Subscriber<T> {

   /**
    * Request a {@link Context} from dependent components which can include downstream
    * operators during subscribing or a terminal {@link org.reactivestreams.Subscriber}.
    *
    * @return a resolved context or {@link Context#empty()}
    */
   default Context currentContext(){
      return Context.empty();
   }
}
```