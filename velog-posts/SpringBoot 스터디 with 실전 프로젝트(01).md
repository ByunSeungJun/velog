<p>친구와 함께 의기투합하여 개발 프로젝트를 진행하고 있다. 1인 개발자로 고군분투하며 가장 크게 의지했던 동료는 다름 아닌 AI였다.</p>
<p>하지만 놀라운 생산성 뒤편으로 '나는 과연 성장하고 있는가?'라는 근본적인 물음이 고개를 들기 시작했다. AI가 짜준 코드를 복사하며, 어느새 기초가 부실한 채로 속도만 좇는 내 자신을 발견했기 때문이다.</p>
<p>이 Velog는 그런 나의 '성장통'에 대한 기록이자, 스스로의 힘으로 단단한 기초를 쌓아가는 여정이다. 앞으로 프로젝트를 병행하며 코드 속에서 가졌던 의문들을 하나씩 파헤치고 정리해나가려 한다. 실전 경험에서 비롯된 학습 기록인 만큼 책처럼 완벽한 순서를 갖추진 못했겠지만, 나와 비슷한 고민을 하는 사람들에게 현실적인 도움이 되기를 바란다.</p>
<p>일단 스프링 부트 개발을 하면서 가장 먼저 알아야할 것은 무엇일까??</p>
<p>바로 <strong>스프링 컨테이너</strong>와 <strong>DI(의존성 주입)</strong>이다.</p>
<h3 id="1-스프링-컨테이너와-bean">1. 스프링 컨테이너와 Bean</h3>
<p>스프링부트 애플리케이션을 실행하면, 가장 먼저 스프링 컨테이너(Spring Container) 라는 보이지 않는 거대한 공간이 생성된다. 이 컨테이너가 하는 가장 중요한 일은, 앞으로 애플리케이션에서 필요한 객체(Object)들을 미리 다 만들어서 보관하고 관리하는 것이다. 스프링 컨테이너가 직접 만들고 관리하는 객체를 <strong>'Bean'</strong>이라고 한다.
-&gt; 이렇게 원래 개발자가 직접 객체를 생성하고 관리해야했으나 스프링 컨테이너로 제어권이 넘어간 것을 알 수 있고 이것을 '제어의 역전'(Inversion of Control, IoC)이라고 한다.</p>
<h3 id="2--어노테이션">2. @ 어노테이션</h3>
<p>어노테이션은 개발자가 스프링 컨테이너에게 &quot;이 클래스는 네가 관리해야 할 이러한 기능을 가진 중요한 객체(Bean)야!&quot;라고 알려주는 일종의 신호이다. </p>
<blockquote>
<p>핵심 어노테이션들</p>
</blockquote>
<ul>
<li><strong>@Component:</strong> 가장 기본적이고 포괄적인 어노테이션</li>
<li><strong>@Controller/@RestController:</strong> @Component의 일종으로 해당 클래스가 외부의 HTTP 요청을 받는 컨트롤러 역할을 하는 Bean이라는 의미를 추가로 가짐</li>
<li><strong>@Service:</strong> @Component의 일종으로 해당 클래스는 비즈니스 로직을 처리하는 서비스 역할을 하는 Bean이라는 의미를 가짐</li>
<li><strong>@Repository:</strong> @Component의 일종으로 해당 클래스는 데이터베이스에 접근하는 역할을 하는 Bean이라는 의미를 가지며, DB 관련 예외를 스프링 예외로 변환해주는 기능도 포함</li>
</ul>
<h4 id="didependancy-injection-의존성-주입">DI(Dependancy Injection, 의존성 주입)</h4>
<p>이제 스프링 컨테이너는Controller, Service, Repository Bean들을 모두 가지고 있다. 그런데 Controller는 Service가 필요하고, Service는 Repository가 필요하다.</p>
<p>이때 개발자가 new UserService() 라고 코드를 짜는 대신, 필요한 곳에 @Autowired 어노테이션을 붙인다.  </p>
<pre><code>@RestController
public class UserController {
    @Autowired // &quot;스프링 컨테이너님, 당신이 가진 UserService 타입의 Bean을 여기에 넣어주세요!&quot;
    private UserService userService;

    // ...
}</code></pre><p>@Autowired: &quot;이 필드(변수)에 스프링 컨테이너가 관리하는 적절한 타입의 Bean을 자동으로 주입해주라는 의미이다. 이것이 바로 의존성 주입(DI)이다.</p>
<blockquote>
<p>정리: @Component 계열 어노테이션으로 Bean을 등록하고, @Autowired로 필요한 곳에 Bean을 주입받아 사용하는 것. 이것이 스프링의 가장 핵심적인 동작 원리이다.</p>
</blockquote>
<h3 id="3-services-vs-repository-명확한-역할-구분">3. Services vs Repository: 명확한 역할 구분</h3>
<p>기존에 헷갈렸던 개념이다. 명확한 역할 구분을 해보자.</p>
<blockquote>
<p><strong>Repository</strong> (창고 관리자):</p>
</blockquote>
<ul>
<li>오직 데이터베이스와의 소통만 담당한다.</li>
<li>DB에 데이터를 저장(save), 조회(find), 수정(update), 삭제(delete)하는 역할에만 집중한다.</li>
<li>마치 창고에서 물건을 넣고 빼는 역할만 하는 직원과 같고 비즈니스 로직은 전혀 모른다.</li>
<li>JpaRepository를 상속(extends)받아 기본적인 CRUD 기능을 구현합니다.</li>
</ul>
<blockquote>
<p><strong>Service</strong> (총괄 셰프):</p>
</blockquote>
<ul>
<li>비즈니스 로직을 총괄한다. '비즈니스 로직'이란 &quot;회원가입 시 쿠폰을 발급한다&quot;, &quot;게시글을 저장하기 전에 제목의 비속어를 검사한다&quot; 와 같은 실제 서비스의 정책과 규칙을 의미한다.</li>
<li>여러 Repository를 호출하여 데이터를 가져오고, 가공하고, 다시 저장하는 등 복잡한 작업을 오케스트레이션한다.</li>
<li>트랜잭션(@Transactional) 관리를 하는 핵심적인 장소이다. (여러 DB 작업을 하나의 단위로 묶어 모두 성공하거나 모두 실패하게 만듦)</li>
<li>컨트롤러에서 요청을 받아, 창고 관리자(Repository)에게 필요한 데이터를 요청하고, 이를 레시피(비즈니스 로직)에 따라 요리하여 다시 컨트롤러에게 돌려주는 셰프와 같다.</li>
</ul>
<p>헷갈렸던 이유: 간단한 기능에서는 Service가 Repository의 메서드를 그대로 호출만 하는 경우가 많기 때문. 하지만 기능이 복잡해질수록 Service의 역할은 명확해짐.</p>
<h3 id="4service-vs-serviceimpl-왜-인터페이스로-분리할까">4.Service vs ServiceImpl: 왜 인터페이스로 분리할까?</h3>
<p>이는 스프링뿐만 아니라 객체 지향 프로그래밍(OOP)의 중요한 원칙과 관련이 있다. 결론부터 말하면 결합도를 낮추고(<strong>느슨한 결합</strong>), 유연하고 <strong>확장 가능한 설계</strong>를 위함이다.</p>
<blockquote>
<h4 id="역할과-구현의-분리">역할과 구현의 분리:</h4>
</blockquote>
<ul>
<li>UserService (인터페이스): &quot;사용자 관련해서는 이런 기능들이 있어&quot; 라고 <strong>'무엇'</strong>을 할 수 있는지 명세(메뉴판)만 정의.</li>
<li>UserServiceImpl (구현 클래스): 인터페이스에 정의된 기능들을 '어떻게' 할 것인지 실제 코드로 구현.</li>
</ul>
<blockquote>
<h4 id="느슨한-결합-loose-coupling">느슨한 결합 (Loose Coupling):</h4>
</blockquote>
<ul>
<li>Controller는 UserServiceImpl 이라는 구체적인 클래스가 아닌, UserService 라는 인터페이스에만 의존함.</li>
<li>장점: 나중에 UserServiceImpl이 아니라 AnotherUserServiceImpl 이라는 새로운 구현체로 바꾸고 싶을 때, Controller 코드는 단 한 줄도 수정할 필요가 없다. 메뉴판(인터페이스)은 그대로 두고 실제 요리사(구현체)만 바꾸는 것과 같다.</li>
</ul>
<blockquote>
<h4 id="테스트-용이성">테스트 용이성:</h4>
</blockquote>
<ul>
<li>Controller를 테스트할 때, 실제 DB까지 연결되는 무거운 UserServiceImpl 대신, 가짜 기능을 수행하는 'Mock(목) 객체'를 만들어 쉽게 테스트할 수 있다.</li>
</ul>
<blockquote>
<h4 id="aop와-프록시">AOP와 프록시:</h4>
</blockquote>
<ul>
<li>스프링의 @Transactional 같은 기능은 실제 ServiceImpl Bean을 감싸는 프록시(Proxy) 객체를 만들어 동작한다. 이런 프록시 기반 기술들은 인터페이스를 사용할 때 훨씬 자연스럽고 강력하게 적용된다.</li>
</ul>
<p>글을 마치며: 이제 스프링부트의 전체적인 뼈대를 살펴보았으니, 앞으로의 글들에서는 각 뼈대에 살을 붙이는 작업을 해보려 한다. 컨트롤러에서 사용하는 @GetMapping, @RequestBody 어노테이션의 상세한 역할부터, Entity와 데이터베이스 테이블을 연결하는 JPA의 @Id, @Column 어노테이션까지, 실제 코드 레벨에서 마주치는 궁금증들을 하나씩 해결하고 기록해나가겠다.</p>