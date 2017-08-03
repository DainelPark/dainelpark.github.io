---
layout: post
title:  "Spring Boot 에서 RedisRepository 사용해보기"
date:   2017-08-03
---

# 1. 개요

다들 알다시피 Spring Boot 의 ORM 인 JPA 는 굉장히 잘되어있다. **@Repository** 와 함께 관련 클래스를 상속하여 사용하면 된다.

아래의 예제 코드를 보는 것이 훨씬 직관적일 것 같다.

	@Repository
	public interface UserRepository extends JpaRepository<UserEntity, Long> {
		Optional<UserEntity> findByUserId(@Param(value = "userId") String userId);
	}
	
> Optional 타입으로 할 경우, 값이 Null 일 때, orElseThrow() 를 사용한다면 쉽게 Null-Safety 한 코드를 작성할 수 있다.

위의 코드의 경우 **userId 와 일치하는 데이터베이스 내부의 정보를 불러오는 것**임을 메서드를 보고 직관적으로 알아낼 수 있다.

Redis 도 이러한 스타일이면 좋겠으나, [Spring Boot Guide](https://spring.io/guides/gs/messaging-redis/) 에 나와있는 코드를 보면 거리가 멈을 알 수 있다.

# 2. 해결

하나의 RedisConfiguration 을 작성해보도록 한다.

	/**
	 * Created by judepark on 2017. 7. 31..
	 * JPA Repository 스타일이 가능하도록 추가.
	 */
	@Configuration
	@EnableRedisRepositories
	public class RedisConfig {

	    @Bean
	    RedisConnectionFactory connectionFactory() {
	        // TODO : Production 일 때, 따로 설정을 추가해줄 것.
	        JedisConnectionFactory factory = new JedisConnectionFactory();
	        return factory;
	    }

	    @Bean
	    RedisTemplate<?, ?> redisTemplate(RedisConnectionFactory connectionFactory) {
	        RedisTemplate<byte[], byte[]> template = new RedisTemplate<>();
	        template.setConnectionFactory(connectionFactory);
	        return template;
	    }
	}
	
**@Configuration 과 @EnableRedisRepositories** 를 주입한다. 

그리고 Entity 를 다음과 같은 방식으로 작성한다.

	@Data
	@AllArgsConstructor
	@NoArgsConstructor
	@RedisHash(value = "status")
	public class Entity {
	    @Id private String id;

	    @NotNull @Indexed private String sourceUserId;
	    @NotNull @Indexed private String destUserId;
	}

이제 개요에서 본 것 처럼 Repository 를 작성하고 메서드를 사용하면 정상적으로 동작이 된다.
