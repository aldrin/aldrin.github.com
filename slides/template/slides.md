## My Presentation Template

### Based on the *awesome* [reveal.js](http://lab.hakim.se/reveal-js/)

<br>
<br>
<br>
ajd.

Note: This is a note which doesn't show up on slides but can keep text you want to have at
hand. Hit 's' to enter the "presenter mode"



### First Slide

- The real content is in a [markdown file](slides.md)
- New slides can be started by 3 empty newlines

~~~ markdown
### First Slide

- The real content is in a [markdown file](slides.md)
- New slides can be started by 3 empty newlines
~~~

Note: Notes can be kept per slide too.




#### XML

~~~ xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="co.aldrin"/>

</beans>
~~~

#### C++

~~~ cpp
#include <iostream>
int main()
{
  std::cout << "hello world!" << std::endl;
}
~~~




### Show pictures, get down to details

<img src="/images/samefringe.png"/>

- If needed, you can get down to details. 
- Literally, like by hitting the down arrow key.


### Details, like this.

The probability of getting $k$ heads when flipping $n$ coins is

$P(E) = {n \choose k} p^k (1-p)^{ n-k}$

and yes, that's MathJax.
