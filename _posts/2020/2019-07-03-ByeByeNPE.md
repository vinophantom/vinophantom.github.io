---
title: Bye Bye NPE
tags: Java Coding 
# article_header:
#   type: cover
#   image:
#     src: /image/java/java-streams.jpg

comments: false

show_author_profile: false

footer: false

mathjax: true
mathjax_autoNumber: false
mermaid: true
chart: true
aside:
    toc: false

modify_date: 2020-01-09

key: byebyenpe_1
full_width: false
lightbox: true
---



>This led me to suggest that the NULL value is a member of every type, and a NULL check is required on every use of that reference variable, and it may be perhaps a billion dollar mistake. [^1]
>-- Tony Hoare 

# What's wrong with NULL

Simply put, NULL reference (or NULL pointer) is a special marker and keyword indicating that something has no value.Programs routinely use NULL pointers to represent conditions such as the end of a list of unknown length or the failure to perform some action; 
this use of NULL pointers can be compared to NULLable types and to the Nothing value in an option type.It is created by Tony Hoare, a famous British computer scientist.

Simply because it was so easy to implement, many computer language designers use NULL reference.
Speaking at a software conference in 2009, Tony Hoare apologized for inventing the NULL reference[^2]

So, what problems does NULL reference cause?


- In these languges, etc Java, NULL is above type checks. It slips through them silently, waiting for runtime, to finally burst free in a shower of errors. 
{% highlight java linenos %}
String s = null;
s.toUppercase(); // compile check success, but crashes at runtime.
{% endhighlight %}
- Null make Ambiguous API.
{% highlight java linenos %}
// name => phone number
Map<String, String> store = new HashMap<>();
store.put("adam", "911"); 
store.put("Bill", null)

store.get("Adam"); // 911 represent Adam's phone number is 911
store.get("Bill"); // null represent Bill don't have a phone number
store.get("Cathy");// null represent ? 
{% endhighlight %}
- NULL is difficult to debug.We do not need say more about it.


__...__[^3]






# NullPointerException in Java

`NullPointerException` 
: `NullPointerException` is an unchecked exception and extends `RuntimeException`.
A `null` value can be assigned to an object reference in Java.

`NullPointerException` is thrown when program attempts to use an object reference that has the null value.

These can be:


- Invoking a method from a `null` object.
- Accessing or modifying a `null` object’s field.
- Taking the length of `null`, as if it were an array.
- Accessing or modifying the slots of `null` object, as if it were an array.
- Throwing `null`, as if it were a Throwable value.
- When you try to synchronize over a `null` object.

__Example__:

{% highlight java linenos %}
// define a String of value null
String a = null

// a is null, does hava a equals method
if (a.equals("OK")) {
    ...
}

{% endhighlight %}

__Output__: 

```
Exception in thread "main" java.lang.NullPointerException
    at Example.main(Example.java:4)
```


# How to avoid NPE
Since Java was created, programmers spent a lot of precious time handling NPE(NullPointerException), which is not some difficult problem.So, let's look at specific ways to solve these problems.


## Check method arguments


{% highlight java linenos %}
void doSomething(String s) {
    ... // some operations
}
{% endhighlight %}
Look at the method `doSomething()`.
Suppose `s` is a null reference, these operations invoke the member methods of argument `s`, 
it will throws a NPE.

To avoid this NPE, we can modify the code as below.

{% highlight java linenos %}

void doSomething(String s) {
    if (s == null) {
        return;
    }
    ... // some operations
}
{% endhighlight %}


## @NotNull
But if we check arguments in each of methods, we may repeat the check operations in different place.So, how do we remind the caller that some arguments can not be null?

Java provides a annotation [`@NotNull`](https://docs.oracle.com/javaee/7/api/javax/validation/constraints/NotNull.html), let us look at how to use it properly.

{% highlight java linenos %}
void doSomething(@NotNull String s) {
    ... // some operations
}

{% endhighlight %}

As the annotation `@NotNull` annotated the argument `s`, developers would pay attention to the variable to the method whether is null.

## Objects

Java 7 provides util type [`Objects`](https://docs.oracle.com/javase/8/docs/api/java/util/Objects.html) in `java.util` package to help developers process on objects.These utilities include null-safe or null-tolerant methods for computing the hash code of an object, returning a string for an object, and comparing two objects.

{% highlight java linenos %}
String a = null;
String b = "YES";

a.equals(b); // NullPointerException

{% endhighlight %}

{% highlight java linenos %}
int[] a = null;
int[] b = new int[] {1, 2, 3, 4, 5};

a.equals(b); // NullPointerException

{% endhighlight %}


{% highlight java linenos %}
String a = null;
String b = "YES";

Objects.equals(a, b); // false

{% endhighlight %}

{% highlight java linenos %}
int[] a = null;
int[] b = new int[] {1, 2, 3, 4, 5};

a.equals(b); // NullPointerException

{% endhighlight %}




## Optional

Java 8 has introduced a new class [`Optional`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) in `java.util` package.It can help in writing a neat code without using too many null checks. 

__Static Method Summary__


|Modifier and Type	    | Method and Description |
|------------------     | ---------------------- |
|static <T> Optional<T> | `empty()` <br> Returns an empty Optional instance.|
|static <T> Optional<T> | `of(T value)` <br> Returns an Optional with the specified present non-null value.|
|static <T> Optional<T> | `ofNullable(T value)` <br> Returns an Optional describing the specified value, if non-null, otherwise returns an empty Optional.|

We can create a `Optional` instance by invoking methods in the table above. 

`ofNullable()` method can accept a null reference.



__Instance Method Summary__

|Modifier and Type      |	Method and Description |
|------------------     | ----------------------- |
|boolean	            | `equals(Object obj)` <br>Indicates whether some other object is "equal to" this Optional.|
|Optional\<T\>	        | `filter(Predicate<? super T> predicate)` <br> If a value is present, and the value matches the given predicate, return an Optional describing the value, otherwise return an empty Optional.|
|\<U\> Optional \<U\>	| `flatMap(Function<? super T, Optional<U>> mapper)` <br> If a value is present, apply the provided Optional-bearing mapping function to it, return that result, otherwise return an empty Optional.|
|T	                    | `get()` <br> If a value is present in this Optional, returns the value, otherwise throws NoSuchElementException.|
|int	                | `hashCode()` <br> Returns the hash code value of the present value, if any, or 0 (zero) if no value is present.|
|void	                | `ifPresent(Consumer<? super T> consumer)` <br>  If a value is present, invoke the specified consumer with the value, otherwise do nothing.|
|boolean	            | `isPresent()` <br> Return true if there is a value present, otherwise false.|
|\<U\> Optional \<U\>	| `map(Function<? super T, ? extends U> mapper)` <br> If a value is present, apply the provided mapping function to it, and if the result is non-null, return an Optional describing the result.|
|T	                    | `orElse(T other)` <br> Return the value if present, otherwise return other.|
|T	                    | `orElseGet(Supplier<? extends T> other)` <br> Return the value if present, otherwise invoke other and return the result of that invocation.|
|\<X extends Throwable\> T | `orElseThrow(Supplier<? extends X> exceptionSupplier)` <br> Return the contained value, if present, otherwise throw an exception to be created by the provided supplier.|



But if you have to write code like this, 

{% highlight java linenos %}
String getUsername(Long id) {
    if (Objects.isNull(id)) {
        return null;
    }
    User user = userDao.get(id);
    if (Objects.isNull(user)) {
        return null;
    }
    String name = user.getName();
    return name;
}  
{% endhighlight %}



{% highlight java linenos %}
String getUsername(Long id) {
    return Optional.ofNullable(id)
        .map(id -> userDao.get(id))
        .map(User::getName)
        .orElse(null);
}
{% endhighlight %}

Suppose there is a 1-to-many relationship between Campaign and Order.
If we use traditional method, check whether variables is null, 
to implement the feature as below;

{% highlight java linenos %}
List<Order> findOrdersByCampaignId (Long id) {
    if (Objects.isNull(id)) {
        return Collections.emptyList();
    }

    List<CampaignOrderRel> rels = 
        campaignOrderRelDao.findByCampaignId(id);

    if (Objects.isNull(rels) || rels.isEmpty()) {
        return Collections.emptyList();
    }

    List<Long> orderIds = rels.stream()
        .map(CampaignOrderRel::getOrder)
        .map(Order::getId)
        .collect(Collectors.toList());
    
    if (Objects.isNull(orderIds)) || orderIds.isEmpty()) {
        return Collections.emptyList();
    }

    return orderDao.findByIds(orderIds);
    
}
{% endhighlight %}

But if we use `Optional`, 

{% highlight java linenos %}
List<Order> findOrdersByCampaignId (Long id) {
    return Optional.ofNullable(id)
        .map(campaignOrderRelDao::findByCampaignId)
        .filter(rels -> !rels.isEmpty())
        .map(rels -> rels.stream()
            .map(CampaignOrderRel::getOrder)
            .map(Order::getId)
            .collect(Collectors.toList()))
        .map(orderDao::findByIds)
        .orElseGet(Collections::emptyList);
}
{% endhighlight %}

By using Optional, we can specify alternate values to return or alternate code to run. This makes the code more readable because the facts which were hidden are now visible to the developer.


## Helpful NullPointerExceptions

While chained statements are nice to look at in the code, they are not NPE friendly. A single statement spread over several lines will give you the line number of the first line in the stack trace regardless of where it occurs.[^4]

{% highlight java %}

    Map<String, String> map = new HashMap<>();
    
    map.get("key").toUpperCase();

{% endhighlight %}

These kind of chained statement will print only “NullPointerException occurred in line number xyz”. It really is hard to debug such code. 

We'll take a look at how JDK 14 and later version, through [JEP 358](https://openjdk.java.net/jeps/358), will solve this issue.

Most importantly, the detailed exception message is switched off by default in JDK 14, on by default in the later version.
To enable it, we need to use the command-line option:

{% highlight shell %}
-XX:+ShowCodeDetailsInExceptionMessages
{% endhighlight %}


When we enable the feature, the exception message prints the local variable name:

```
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "String.toUpperCase()" because the return value of "java.util.Map.get(Object)" is null
	at com.jsprx.jdk15.HelpfulNullPointerException.main(HelpfulNullPointerException.java:12)

```




[^1]: [Null References: The Billion Dollar Mistake](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/)
[^2]: [Youtube - Null References: The Billion Dollar Mistake - Tony Hoare](https://www.youtube.com/watch?v=ybrQvs4x0Ps)
[^3]: [THE WORST MISTAKE OF COMPUTER SCIENCE - Paul Draper](https://www.lucidchart.com/techblog/2015/08/31/the-worst-mistake-of-computer-science/)
[^4]: [Java NullPointerException – How to effectively handle null pointer in Java](https://howtodoinjava.com/java/exception-handling/how-to-effectively-handle-nullpointerexception-in-java/)
