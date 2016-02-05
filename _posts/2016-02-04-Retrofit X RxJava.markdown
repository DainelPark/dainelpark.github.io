---
layout: post
title:  "Retrofit x RxJava를 이용한 네트워크 통신"
date:   2016-02-04 12:10:00
summary: Retrofit X RxJava
---

안드로이드에는 여러 Rest Client가 존재한다. 예를 들면, 구글의 Volley, Square의 Retrofit 등이 있다.

이번 프로젝트에서는 Retrofit을 사용하게 되었는데 한 번 RxJava와 같이 써보기로 결정하였다.

Gradle에 RetroLambda, RxAndroid, RxJava, Retrofit 등, 구현에 필요한 Dependency가 입력되어있다는 가정 하에 글을 작성해본다.

	RestClient.java

	private static Retrofit mRetrofit;
    private static String BASE_URL = "";

    public static void init() {

        HttpLoggingInterceptor loggin = new HttpLoggingInterceptor();
        loggin.setLevel(HttpLoggingInterceptor.Level.BODY);

        OkHttpClient.Builder httpClient = new OkHttpClient.Builder();
        httpClient.interceptors().add(loggin);

        mRetrofit = new Retrofit.Builder()
                .baseUrl(BASE_URL)
                .client(httpClient.build())
                .addConverterFactory(GsonConverterFactory.create())
                .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
                .build();
    }

    public static Retrofit getRestClient() {
        return mRetrofit;
    }

다음과 같이 코드를 작성해주자.

위의 HttpLogginIntercepter를 이용해서 OkHttpClient를 Build한다. (Retrofit 2.0에선 Default HTTP Client가 OkHttp의 최신 버전이다.) 안해주면 로깅이 안된다. 따로 Intercepter를 implements 해서 작성하여도 된다.
 **RxJavaCallAdapterFactory.create()** 부분이 RxJava를 사용할 수 있도록 해주는 부분이다. 이제 Call<T>를 Observable<T>로 작성할 수 있다.
 
	@Override
	public void onCreate() {
    	super.onCreate();
    	RestClient.init();
	}
	
	
Application 클래스의 onCreate에 RestClient.init();을 해준다.

	public interface UserService {
		@GET(url)
		Observable<JsonObject> me();
	}

다음과 같이 API 구성을 위한 인터페이스를 작성해준다. **(인터페이스가 아니면 절대로 진행되지 않도록 되어있다.)** RxJava를 사용하기 전과 달라진 점이라면 Call<T> -> Observable<T>로 되었다는 점이다.

	RestClient.getRestClient()
				.create(UserService.class)
				.me()
				.subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .unsubscribeOn(Schedulers.io())
                .retry(3)
                .subscribe(
                	(response) -> {
                		// do action with response
                	},
                	(throwable) -> {
                		// when error occurs, do action
                	},
                	() -> {
                		// when request completed, do action
                	}
                );


다음과 같이 작성하면 된다. subscribe의 3개의 오버라이드 함수는 각각 onNext, onError, onCompleted 이다. 람다 표현식을 이용하여 간략하게 하였을 뿐이다. 보면 메서드의 이름부터 사용 용도가 보인다. 용도에 맞게 잘 사용하면 된다. retry(3)은 onError에 도달하였을 때, 재시도를 하는 부분이다. 우리가 앱을 사용할 때 네트워크가 불안정한 상태에서 재시도가 발생하지 않는가? 그 때 사용되는 메서드다.

나는 AbstractApiClient를 extends하고 UserService를 implements한 UserClient라는 클래스를 하나 만들어서 사용하는 편이다. 옳게 쓰는 진 잘 모르겠다.

이렇게 보면 RxJava가 쉬워보이지만 그렇지는 않다. 러닝 커브가 빠르게 진행되진 않는다. 좀 더 공부하자.