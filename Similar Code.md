```java
public class TestableHtml {
  public static String testableHtml(
    PageData pageData,
    boolean includeSuiteSetup) throws Exception {
    WikiPage wikiPage = pageData.getWikiPage();
    StringBuffer buffer = new StringBuffer();
    if (pageData.hasAttribute("Test")) {
      if (includeSuiteSetup) {
        WikiPage suiteSetup =
          PageCrawlerImpl.getInheritedPage(
            SuiteResponder.SUITE_SETUP_NAME, wikiPage);
        if (suiteSetup != null) {
          WikiPagePath pagePath =
            suiteSetup.getPageCrawler().getFullPath(suiteSetup);
          String pagePathName = PathParser.render(pagePath);
          buffer.append("!include -setup .")
            .append(pagePathName)
            .append("\n");
        }
      }
      WikiPage setup =
        PageCrawlerImpl.getInheritedPage("SetUp", wikiPage);
      if (setup != null) {
        WikiPagePath setupPath =
          setup.getPageCrawler().getFullPath(setup);
        String setupPathName = PathParser.render(setupPath);
        buffer.append("!include - setup.")
          .append(setupPathName)
          .append("\n");
      }
    }
    buffer.append(pageData.getContent());
    if (pageData.hasAttribute("Test")) {
      WikiPage teardown =
        PageCrawlerImpl.getInheritedPage("TearDown", wikiPage);
      if (teardown != null) {
        WikiPagePath tearDownPath =
          teardown.getPageCrawler().getFullPath(teardown);
        String tearDownPathName = PathParser.render(tearDownPath);
        buffer.append("\n")
          .append("!include - teardown.")
          .append(tearDownPathName)
          .append("\n");
      }
      if (includeSuiteSetup) {
        WikiPage suiteTeardown =
          PageCrawlerImpl.getInheritedPage(
            SuiteResponder.SUITE_TEARDOWN_NAME,
            wikiPage
          );
        if (suiteTeardown != null) {
          WikiPagePath pagePath =
            suiteTeardown.getPageCrawler().getFullPath(suiteTeardown);
          String pagePathName = PathParser.render(pagePath);
          buffer.append("!include -teardown .")
            .append(pagePathName)
            .append("\n");
        }
      }
      pageData.setContent(buffer.toString());
      return pageData.getHtml();
    }
  }
}
```

```java
public class TestableHtml {
  public static String testableHtml(
    PageData pageData,
    boolean includeSuiteSetup) throws Exception {
    WikiPage wikiPage = pageData.getWikiPage();
    StringBuffer buffer = new StringBuffer();
    if (pageData.hasAttribute("Test")) {
      if (includeSuiteSetup) {
        includeInheritedPage(SuiteResponder.SUITE_SETUP_NAME,
          wikiPage, buffer, "!include -setup .");
      }
      includeInheritedPage("SetUp", wikiPage, buffer,
                           "!include -setup .");
    }
    buffer.append(pageData.getContent());
    if (pageData.hasAttribute("Test")) {
      includeInheritedPage("TearDown", wikiPage, buffer,
                           "\n!include -teardown .");
      if (includeSuiteSetup) {
        includeInheritedPage(SuiteResponder.SUITE_TEARDOWN_NAME,
          wikiPage, buffer, "!include -teardown .");
      }
    }
    pageData.setContent(buffer.toString());
    return pageData.getHtml();
  }

  private static void
  includeInheritedPage(String pageName, WikiPage wikiPage,
                      StringBuffer buffer,
                      String includeString) {
    WikiPage inheritedPage =
      PageCrawlerImpl.getInheritedPage(pageName, wikiPage);
    if (inheritedPage != null) {
      WikiPagePath pagePath =
        inheritedPage.getPageCrawler().getFullPath(inheritedPage);
      String pagePathName = PathParser.render(pagePath);
      buffer.append(includeString)
        .append(pagePathName)
        .append("\n");
    }
  }
}
```

```java
public class TestableHtml {

  private static StringBuffer buffer;
  private static WikiPage wikiPage;

  public static String testableHtml(
    PageData pageData,
    boolean includeSuiteSetup) throws Exception {
    wikiPage = pageData.getWikiPage();
    buffer = new StringBuffer();
    if (pageData.hasAttribute("Test")) {
      if (includeSuiteSetup) {
        includeInheritedPage(SuiteResponder.SUITE_SETUP_NAME,
          "!include -setup .");
      }
      includeInheritedPage("SetUp", "!include -setup .");
    }
    buffer.append(pageData.getContent());
    if (pageData.hasAttribute("Test")) {
      includeInheritedPage("TearDown", "\n!include -teardown .");
      if (includeSuiteSetup) {
        includeInheritedPage(SuiteResponder.SUITE_TEARDOWN_NAME,
          "!include -teardown .");
      }
    }
    pageData.setContent(buffer.toString());
    return pageData.getHtml();
  }

  private static void
  includeInheritedPage(String pageName, String includeString) {
    WikiPage inheritedPage =
      PageCrawlerImpl.getInheritedPage(pageName, wikiPage);
    if (inheritedPage != null) {
      WikiPagePath pagePath =
        inheritedPage.getPageCrawler().getFullPath(inheritedPage);
      String pagePathName = PathParser.render(pagePath);
      buffer.append(includeString)
        .append(pagePathName)
        .append("\n");
    }
  }
}
```
