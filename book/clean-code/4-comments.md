# 4. Comments

- [4. Comments](#4-comments)
  - [Introduction](#introduction)
  - [Comments Do Not Make Up for Bad Code](#comments-do-not-make-up-for-bad-code)
  - [Explain Yourself in Code](#explain-yourself-in-code)
  - [Good Comments](#good-comments)
    - [Legal Comments](#legal-comments)
    - [Informative Comments](#informative-comments)
    - [Explanation of Intent](#explanation-of-intent)
    - [Clarification](#clarification)
    - [Warning of Consequences](#warning-of-consequences)
    - [TODO Comments](#todo-comments)
    - [Amplification](#amplification)
    - [Javadocs in Public APIs](#javadocs-in-public-apis)
  - [Bad Comments](#bad-comments)
    - [Mumbling](#mumbling)
    - [Redundant Comments](#redundant-comments)
    - [Misleading Comments](#misleading-comments)
    - [Mandated Comments](#mandated-comments)
    - [Journal Comments](#journal-comments)
    - [Noise Comments](#noise-comments)
    - [Scary Noise](#scary-noise)
    - [Don’t Use a Comment When You Can Use a Function or a Variable](#dont-use-a-comment-when-you-can-use-a-function-or-a-variable)
    - [Position Markers](#position-markers)
    - [Closing Brace Comments](#closing-brace-comments)
    - [Attributions and Bylines](#attributions-and-bylines)
    - [Commented-Out Code](#commented-out-code)
    - [HTML Comments](#html-comments)
    - [Nonlocal Information](#nonlocal-information)
    - [Too Much Information](#too-much-information)
    - [Inobvious Connection](#inobvious-connection)
    - [Function Headers](#function-headers)
    - [Javadocs in Nonpublic Code](#javadocs-in-nonpublic-code)

## Introduction

- Don’t comment bad code — rewrite it
- comment는 필요악임. 표현력이 충분히 좋다면 comment가 필요 없음. comment는 항상 실패임.
- 프로그래머가 comment를 관리를 잘 안해서 오래될수록 구라를 칠 확률이 높음. 코드로 표현해라.

## Comments Do Not Make Up for Bad Code

- 주석은 나쁜 코드를 덮기 위해서 있는게 아니다. 주석을 달아야 할거 같으면 새로 짜라.

## Explain Yourself in Code

- 코드의 의도를 표현해라.
  ```java
  // bad
  // Check to see if the employee is eligible for full benefits
  if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))

  // good
  if (employee.isEligibleForFullBenefits())
  ```

## Good Comments

- 가끔은 필요한 주석이 있긴 하다. 하지만 주석이 없는게 최고인걸 명심할 것.

### Legal Comments

- 라이선스 정보 등을 위해 다는 주석은 필요. 그렇다고 라이선스 전문을 쓰진 말 것.
  ```java
  // Copyright (C) 2003,2004,2005 by Object Mentor, Inc. All rights reserved.
  // Released under the terms of the GNU General Public License version 2 or later.
  ```

### Informative Comments

- 정보정 주석은 도움이 되긴 한다. But 함수명을 바꾸는게 최고.
  ```java
  // not bad
  // Returns an instance of the Responder being tested.
  protected abstract Responder responderInstance();

  // good
  protected abstract Responder responderBeingTested();
  ```

### Explanation of Intent

- 결정사항 등 의도를 파악하기 위한 주석은 도움이 됨.
  ```java
  public int compareTo(Object o) {
    if (o instanceof WikiPagePath) {
      WikiPagePath p = (WikiPagePath) o;
      String compressedName = StringUtil.join(names, "");
      String compressedArgumentName = StringUtil.join(p.names, "");
      return compressedName.compareTo(compressedArgumentName);
    }
    return 1; // we are greater because we are the right type.
  }
  ```
  ```java
  public void testConcurrentAddWidgets() throws Exception {
    WidgetBuilder widgetBuilder = new WidgetBuilder(new Class[]{BoldWidget.class});
    String text = "'''bold text'''";
    ParentWidget parent = new BoldWidget(new MockWidgetRoot(), "'''bold text'''");
    AtomicBoolean failFlag = new AtomicBoolean();
    failFlag.set(false);

    //This is our best attempt to get a race condition
    //by creating large number of threads.
    for (int i = 0; i < 25000; i++) {
      WidgetBuilderThread widgetBuilderThread =
      new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
      Thread thread = new Thread(widgetBuilderThread);
      thread.start();
    }
    assertEquals(false, failFlag.get());
  }
  ```

### Clarification

- 리턴값이나 인자를 더이상 명료하게 표현하지 못할 경우 주석은 도움이 됨.
```java
public void testCompareTo() throws Exception {
  WikiPagePath a = PathParser.parse("PageA");
  WikiPagePath ab = PathParser.parse("PageA.PageB");
  WikiPagePath b = PathParser.parse("PageB");
  WikiPagePath aa = PathParser.parse("PageA.PageA");
  WikiPagePath bb = PathParser.parse("PageB.PageB");
  WikiPagePath ba = PathParser.parse("PageB.PageA");

  assertTrue(a.compareTo(a) == 0);    // a == a
  assertTrue(a.compareTo(b) != 0);    // a != b
  assertTrue(ab.compareTo(ab) == 0);  // ab == ab
  assertTrue(a.compareTo(b) == -1);   // a < b
  assertTrue(aa.compareTo(ab) == -1); // aa < ab
  assertTrue(ba.compareTo(bb) == -1); // ba < bb
  assertTrue(b.compareTo(a) == 1);    // b > a
  assertTrue(ab.compareTo(aa) == 1);  // ab > aa
  assertTrue(bb.compareTo(ba) == 1);  // bb > ba
}
```

### Warning of Consequences

- 경고의 의미에서 하는 주석도 필요한 경우가 있음.
  ```java
  public static SimpleDateFormat makeStandardHttpDateFormat()
  {
    //SimpleDateFormat is not thread safe,
    //so we need to create each instance independently.
    SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
    df.setTimeZone(TimeZone.getTimeZone("GMT"));
    return df;
  }
  ```

### TODO Comments

- 미래에 해야 할 일을 적어두는 경우에도 좋음.
  ```java
  //TODO-MdM these are not needed
  // We expect this to go away when we do the checkout model
  protected VersionInfo makeVersion() throws Exception {
    return null;
  }
  ```
- 요즘 ide는 todo 주석도 tracking해줌.

### Amplification

- 얼핏 보면 사소해 보이는 코드의 의도를 중요하게 할 때도 좋음.
  ```java
  String listItemContent = match.group(3).trim();
  // the trim is real important. It removes the starting
  // spaces that could cause the item to be recognized
  // as another list.
  new ListItemWidget(this, listItemContent, this.level + 1);
  return buildList(text.substring(match.end()));
  ```

### Javadocs in Public APIs

- Public API에서 Javadoc 같은 문서는 필요.

## Bad Comments

### Mumbling

### Redundant Comments

### Misleading Comments

### Mandated Comments

### Journal Comments

### Noise Comments

### Scary Noise

### Don’t Use a Comment When You Can Use a Function or a Variable

### Position Markers

### Closing Brace Comments

### Attributions and Bylines

### Commented-Out Code

### HTML Comments

### Nonlocal Information

### Too Much Information

### Inobvious Connection

### Function Headers

### Javadocs in Nonpublic Code

---

> 이 책에서 제일 논란의 여지가 없을 장 같음. 최고의 주석은 없는 주석이다.