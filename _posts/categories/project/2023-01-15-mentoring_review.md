---
title:  "멘토링 리뷰 관련 정리 및 앞으로의 방향성"
categories:
  - Project
date: 2023-01-15
---

# 🥞 정리
---
- TDD , S X Q = R X T

- 30년간 지속된 30가지 SW공학 이슈: 🔗http://dientra.egloos.com/m/5283534

```
1. 초기 요구사항의 50% 이상이 완전하지 못하다.
2. 요구사항은 개발 중에 한달 평균 약 2% 정도 씩 증가한다.
3. 초기 요구사항의 약 20%는 두 번째 이후 명확해진다.
4. 버그를 발견하고 조치하는 것이 가장 비싼 소프트웨어 활동이다.
5. 문서를 작성하는 것은 두 번째로 비싼 소프트웨어 활동이다.
6. 코딩은 세 번째로 비싼 소프트웨어 활동이다.
7. 회의와 논의가 네 번째로 비싼 활동이다.
8. 대부분의 테스팅 형식은 버그를 발견하는데 30% 미만의 효율성을 보인다.
9. 대부분의 테스팅 형식은 테스트될 코드의 50% 미만을 터치한다.
10. 소스코드보다는 요구사항과 설계에 더 많은 결함이 존재한다.
11. 소프트웨어 자체보다는 테스트 케이스에 더 많은 결함이 존재한다.
12. 요구사항, 설계 및 코드에 FP당 평균 5개의 결함이 존재한다.
13. 배포전 전체 결함 제거 효율성은 평균적으로 약 85% 수준이다.
14. 소프트웨어 결함의 약 15%는 고객에게 전달된다.
15. 고객에게 전달된 결함은 조치 비용이 비싸고 고객 불만을 야기시킨다.
16. 약 5%의 응용프로그램이 전체 결함의 50%를 내포한다.
17. 모든 결함 조치 중 약 7%에 새로운 결함이 내포된다.
18. 소프트웨어 재사용이 결함율 0에 접근할 수 있는 유일한 효과적인 방법이다.
19. 소프트웨어 아웃소싱 계약의 약 5%가 법정 다툼까지 간다.
20. 1만FP 이상 프로젝트의 약 35%가 취소된다.
21. 1만FP 이상 프로젝트의 약 50%가 1년 정도 지연된다.
22. 대부분의 비용 견적 실패는 지나치게 낙관적인 것에 기인한다.
23. 미국에서 생산성은 1MM당 약 10FP 이다.
24. 개발 할당 범위는 1인당 약 150FP이다.
25. 유지보수 할당 범위는 1인당 약 750FP이다.
26. 미국에서 FP당 개발비용은 약 1200불이다.
27. 미국에서 FP당 연 유지보수 비용은 약 150불이다.
28. 응용시스템을 전달한 이후 사용하면서 1년에 약 7%정도 증가한다.
29. 평균 결함제거율은 1개월에 약 10개정도이다.
30. 프로그래머는 현재를 유지하기 위하여 연평균 약 10일의 교육이 필요하다.
```

- 클린코드 관련 github : 🔗https://github.com/Yooii-Studios/Clean-Code

- 함께 일하고 싶은 개발자 : 🔗https://yozm.wishket.com/magazine/detail/1702/

- 페어프로그래밍 회고 : 🔗https://pjh3749.tistory.com/247

- Spring - Field vs Constructor vs Setter Injection \
 그리고 순환참조(Circular Reference): \
🔗https://coding-start.tistory.com/250

- TDD

```java

package com.mamoorie.mytraview.service;

import static org.hamcrest.MatcherAssert.assertThat;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNull;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.times;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

import com.mamoorie.mytraview.entity.Article;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import com.mamoorie.mytraview.repository.ArticleRepository;

import java.util.Optional;

@ExtendWith(MockitoExtension.class)
class ArticleServiceTest {

    @Mock
    private ArticleRepository articleRepository;

    @InjectMocks
    private ArticleService articleService;

    @Test
    void findAllArticles_callArticleServiceRepository() {
        articleService.findAllArticles();

        verify(articleRepository, times(1)).findAll();
    }

    @Test
    void findById_callRepistory() {
        int iD = 2;
        articleService.findById(iD);
        verify(articleRepository).findById(2);
    }

    @Test
    void findById_returnArticle() {
        //given
        int iD = 2;
        Article returnArticle = Article.builder()
                .id(2)
                .title("test")
                .build();
        when(articleRepository.findById(iD)).thenReturn(Optional.of(returnArticle));

        //when
        Article article = articleService.findById(iD);

        //then
        assertEquals(article,returnArticle);

    }

    @Test
    void findById_returnNull_whenRepositoryIsEmpty() {
        //given
        int iD = 2;

        when(articleRepository.findById(iD)).thenReturn(Optional.empty());

        //when
        Article article = articleService.findById(iD);

        //then
        assertNull(article);

    }

}

```

```
르블랑의 법칙 :
일정에 맞추기 위해 나쁜 코드들을 방치하고는 '나중에 고쳐야지'라고 생각한 경험이 다들 있을 것이다. 하지만
Later equals never - LeBlanc's law (나중은 절대 오지 않는다 - 르블랑의 법칙)

보이스카우트 원치 :
시간이 지날 수록 더러워지는 코드를 본 적이 있을 것이다. 미국 보이스카우트에는 이러한 상황에 사용할 수 있는 단순한 규칙이 하나 있다.
"Leave the campground cleaner than you found it."


디미터 법칙 :
잘 알려진 휴리스틱heuristic(경험에 기반하여 문제를 해결하거나 학습하거나 발견해 내는 방법)으로, 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙이다.
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

# 앞으로의 방향

이제 시작이라고 생각한다.
시간이 조급했던 파이널 프로젝트, 그리고 이제 부트캠프의 마지막을 맺는 이 시점에서 나는 더 성장해나가야 한다.
코테라는 부분도 있겠고, CS 지식도 단단히 챙겨야 할 것이며, 백, 프론트엔드 부분은 다 다루고 싶다는 욕심도 있다.

물론 많이 기울여져있는 부분은 프론트엔드이지만, 아무래도 백엔드가 수요가 많은것은 현실이라서 백엔드 쪽을 준비를 하되, 향후 생각해보는 것은 프론트엔드도 손을 놓기가 싫기에 풀스택을 목표로 생각하고 있다.

코테부터 먼저 통과할 수 있을 정도로의 실력을 만들어두고, 그 뒤로 면접 관련한 것들을 생각할 수 있으니까, 힘내보자!👀

2023.01.15 새벽 2시 30분




