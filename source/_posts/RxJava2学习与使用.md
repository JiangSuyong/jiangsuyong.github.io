---
title: RxJava2学习与使用
date: 2017-05-01 01:32:04
tags: RxJava2
categories: Android
---

#            RxJava2学习与使用

RxJava可以说在java中相当流行的一个库了。RxJava目前分RxJava1与RxJava2两个版本。这是它的github地址:[github.com/ReactiveX/RxJava](https://github.com/ReactiveX/RxJava)。

RxJava2基于[reactive-streams](http://www.reactive-streams.org)重写的。这是Java实现的reactive-streams的github路径[https://github.com/reactive-streams/reactive-streams-jvm](https://github.com/reactive-streams/reactive-streams-jvm)。我们来看reactive-streams-jvm的api接:![image](http://op7g58imx.bkt.clouddn.com/RxJava2%E5%AD%A6%E4%B9%A0%E4%B8%8E%E4%BD%BF%E7%94%A8RxJava2-1.png)

这里面仅仅有4个接口
```
/**
 * A Processor represents a processing stage—which is both a {@link Subscriber}
 * and a {@link Publisher} and obeys the contracts of both.
 *
 * @param <T> the type of element signaled to the {@link Subscriber}
 * @param <R> the type of element signaled by the {@link Publisher}
 */
public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {
}
```

```
/**
 * A {@link Publisher} is a provider of a potentially unbounded number of sequenced elements, publishing them according to
 * the demand received from its {@link Subscriber}(s).
 * <p>
 * A {@link Publisher} can serve multiple {@link Subscriber}s subscribed {@link #subscribe(Subscriber)} dynamically
 * at various points in time.
 *
 * @param <T> the type of element signaled.
 */
public interface Publisher<T> {

    /**
     * Request {@link Publisher} to start streaming data.
     * <p>
     * This is a "factory method" and can be called multiple times, each time starting a new {@link Subscription}.
     * <p>
     * Each {@link Subscription} will work for only a single {@link Subscriber}.
     * <p>
     * A {@link Subscriber} should only subscribe once to a single {@link Publisher}.
     * <p>
     * If the {@link Publisher} rejects the subscription attempt or otherwise fails it will
     * signal the error via {@link Subscriber#onError}.
     *
     * @param s the {@link Subscriber} that will consume signals from this {@link Publisher}
     */
    public void subscribe(Subscriber<? super T> s);
}
```

```
/**
 * Will receive call to {@link #onSubscribe(Subscription)} once after passing an instance of {@link Subscriber} to {@link Publisher#subscribe(Subscriber)}.
 * <p>
 * No further notifications will be received until {@link Subscription#request(long)} is called.
 * <p>
 * After signaling demand:
 * <ul>
 * <li>One or more invocations of {@link #onNext(Object)} up to the maximum number defined by {@link Subscription#request(long)}</li>
 * <li>Single invocation of {@link #onError(Throwable)} or {@link Subscriber#onComplete()} which signals a terminal state after which no further events will be sent.
 * </ul>
 * <p>
 * Demand can be signaled via {@link Subscription#request(long)} whenever the {@link Subscriber} instance is capable of handling more.
 *
 * @param <T> the type of element signaled.
 */
public interface Subscriber<T> {
    /**
     * Invoked after calling {@link Publisher#subscribe(Subscriber)}.
     * <p>
     * No data will start flowing until {@link Subscription#request(long)} is invoked.
     * <p>
     * It is the responsibility of this {@link Subscriber} instance to call {@link Subscription#request(long)} whenever more data is wanted.
     * <p>
     * The {@link Publisher} will send notifications only in response to {@link Subscription#request(long)}.
     * 
     * @param s
     *            {@link Subscription} that allows requesting data via {@link Subscription#request(long)}
     */
    public void onSubscribe(Subscription s);

    /**
     * Data notification sent by the {@link Publisher} in response to requests to {@link Subscription#request(long)}.
     * 
     * @param t the element signaled
     */
    public void onNext(T t);

    /**
     * Failed terminal state.
     * <p>
     * No further events will be sent even if {@link Subscription#request(long)} is invoked again.
     *
     * @param t the throwable signaled
     */
    public void onError(Throwable t);

    /**
     * Successful terminal state.
     * <p>
     * No further events will be sent even if {@link Subscription#request(long)} is invoked again.
     */
    public void onComplete();
}
```
```
/**
 * A {@link Subscription} represents a one-to-one lifecycle of a {@link Subscriber} subscribing to a {@link Publisher}.
 * <p>
 * It can only be used once by a single {@link Subscriber}.
 * <p>
 * It is used to both signal desire for data and cancel demand (and allow resource cleanup).
 *
 */
public interface Subscription {
    /**
     * No events will be sent by a {@link Publisher} until demand is signaled via this method.
     * <p>
     * It can be called however often and whenever needed—but the outstanding cumulative demand must never exceed Long.MAX_VALUE.
     * An outstanding cumulative demand of Long.MAX_VALUE may be treated by the {@link Publisher} as "effectively unbounded".
     * <p>
     * Whatever has been requested can be sent by the {@link Publisher} so only signal demand for what can be safely handled.
     * <p>
     * A {@link Publisher} can send less than is requested if the stream ends but
     * then must emit either {@link Subscriber#onError(Throwable)} or {@link Subscriber#onComplete()}.
     * 
     * @param n the strictly positive number of elements to requests to the upstream {@link Publisher}
     */
    public void request(long n);

    /**
     * Request the {@link Publisher} to stop sending data and clean up resources.
     * <p>
     * Data may still be sent to meet previously signalled demand after calling cancel.
     */
    public void cancel();
}
```
说了这么多题外话，我们来看看Hello World program:

```
package rxjava.examples;

import io.reactivex.*;

public class HelloWorld {
    public static void main(String[] args) {
        Flowable.just("Hello world").subscribe(System.out::println);
    }
}
```
下面是我摘抄README的描述，可以看出下面那几个类的大致特性
### RxJava 2 features several base classes you can discover operators on:

- io.reactivex.Flowable : 0..N flows, supporting Reactive-Streams and backpressure
- io.reactivex.Observable: 0..N flows, no backpressure
- io.reactivex.Single: a flow of exactly 1 item or an error
- io.reactivex.Completable: a flow without items but only a completion or error signal
- io.reactivex.Maybe: a flow with no items, exactly one item or an error

对于这些个描述，我们可以根据这些来具体如何使用这些个类了。






最后，谢谢前人开发出这么好的库给我们使用，同时也感谢网络上编写相关博文的人，让我了解和使用RxJava2。

参考：
1. https://github.com/ReactiveX/RxJava
2. 拉丁吴 [关于RxJava最友好的文章](http://www.jianshu.com/u/1d8042233f67)
3. 许矶法 [RxJava2 常用方法介绍](https://blog.xujifa.cn/2017/02/22/rxjava/)
4. 网络上其他博文，这里不一一列举










