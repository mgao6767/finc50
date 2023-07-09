---
hide:
  - navigation
  - footer
---

# Welcome to FINC50

**FINC50** is a finance course before even your Finance 101. (1)
{ .annotate }

1. 🙋‍♂️ 50 is a half of 101, rounded down.

*[Finance 101]: "101" represents a basic beginning course.

## About FINC50

FINC50 is a comprehensive website designed to provide an interactive learning experience for students in undergraduate and postgraduate finance courses. The platform serves as a valuable resource for understanding and exploring modern finance topics while leveraging cutting-edge web-based technologies. Our goal is to bridge the gap between theory and practice by offering engaging and interactive content that complements traditional coursework.

## Features and Benefits

<div class="grid cards" markdown>

-   :material-file-edit-outline:{ .lg .middle } **Supplement to Traditional Courses**

    ---

    FINC50 serves as a complement to traditional finance courses by offering supplementary materials, practical examples, and additional practice exercises.

    [:octicons-arrow-right-24: Get started](./courses)

-   :material-scale-balance:{ .lg .middle } **Open Source, MIT**

    ---

    FINC50 is licensed under MIT and available on [GitHub](https://github.com/mgao6767/finc50).

    [:octicons-arrow-right-24: License](https://github.com/mgao6767/finc50/blob/main/LICENSE)

-   :material-chart-line:{ .lg .middle } **Interactive Learning**

    ---

    FINC50 embraces interactivity for more enjoyable learning.

    ```vegalite
    {%
       include "./vega/sp500_stock_return_volatility.json"
    %}
    ```

    For example, a graph showing volatility and return of S&P500 constituents in 2022.(1)
    Try to pan, zoom, select and click.
    { .annotate }
    
    1. The data is retrieved using the following Python code.
       ```python
       {%
          include "./data/spy_risk_return.py"
       %}  
       ```

    [:octicons-arrow-right-24: Read more](./courses/principle-of-finance/risk-and-return)

-   :material-notebook-outline:{ .lg .middle } **Comprehensive Coverage**

    ---

    FINC50 covers a wide range of modern finance topics, including
    
    - [principle of finance](./courses/principle-of-finance),
    - [corporate finance](./courses/corporate-finance),
    - investment analysis,
    - risk management,
    - financial markets,
    - and more.
    
    Our content aligns with the curriculum of undergraduate and postgraduate finance courses.

    [:octicons-arrow-right-24: Check out courses](./courses)

</div>

## Get Started

We invite you to explore the various sections of FINC50 and dive into the world of modern finance. Whether you are a student, an educator, or a finance enthusiast, our platform offers valuable resources to enhance your learning and teaching experience.

Thank you for joining us on this exciting journey of finance education and interactive learning!

[Check out courses :material-bookshelf:](./courses){ .md-button .md-button--primary }

---
