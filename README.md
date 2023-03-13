# [XPath](https://youtube.com/playlist?list=PLmRg3gEG2XIackdOpGvb_jEX1ywaplUmh): Its uses and caveats in Appium

XPath |
---- |
A query language used to identify nodes in an XML document |
Since Appium builds an XML representation of your app, XPath can be used with it. |
XPath is powerful but can be dangerous. |

XPath is not something Appium invented. XPath is a query language designed for use with searching XML documents. An XPath engine allows you to use an XPath query in conjunction with a particular XML document in order to find specific XML nodes you're interested in. 

Appium has an XML representation of your app. What this means is that we can use XPath queries on that XML document to specify individual UI elements in your app. Because of the way XPath works, it is an extremely flexible and powerful way of finding elements. However, it comes with some caveats and downsides that you should be aware of any time you consider using XPath. 

Benefits of XPath |
---- |
Can be used to find any element that exists |
Can be used to find elements using complex criteria (for example, finding a node by partial text). |
<code>MobileBy.XPATH (""//android.widget.TextView[contains(@text, 'You are logged in')]) |

What are the reasons you'd want to use XPath to find your elements with Appium? 
1. The main reason is that for any element that exists in your app hierarchy there is guaranteed to be some XPath query, which can find it. In other words, given an XML document every node can be found by one or more XPath queries. So if you need to automate an element that doesn't have an accessibility ID, for example, you know that you can always use XPath to find it.
2. The other main benefit is that XPath is a powerful query language that enables complex filtering of criteria. For example, we don't necessarily know what user name our test will use to log in, but we know that a certain portion of the element's text will contain, you are logged in. So we use the XPath contain function to find that element. 

Dangers of XPath |
---- |
XPath selectors can be brittle. |
//android.widget.Layout/android.widget.Layout[3]/android.widget.Layout/android.widget.TextView[2] |
XPath can be slow. |

Unfortunately, XPath does not come without certain costs. 
1. The first problem with XPath is that it can encourage us to use brittle selectors. Brittle selectors are selectors which we use to find the elements while our test is being developed, but then stop finding the element successfully on subsequent test runs or for apps updated. This is an example of a brittle selector. Why is it brittle? This XPath query finds the second TextView underneath a layout, underneath the third layout of any other layout. When I'm writing my test, this might very well find the element I want, but it relies completely on the hierarchical structure of my app. If the app developer changes any of the layout at all or, if I run the app in a tablet form factor, let's say instead of a phone form factor, it's highly probable that this XPath query will not find the element I want any longer. Even worse, it might find a completely different element, which would cause no end to my confusion while trying to debug what was going on. 
2. The other main reason to think twice before reaching for XPath is that finding elements using XPath within Appium can be slow. This is the case because neither Android apps, nor iOS apps, are internally represented as XML documents. When it comes to running an XPath query in your app, Appium actually reads the state of the app, turns it into an XML document, runs the XPath query, finds any matching nodes, and then turns those XML nodes back into native UI element objects. It's a lot. Each of these steps can potentially be expensive especially the step of generating the XML document in the first place. Basically, the more elements you have in your app view, the longer finding anything using XPath will take. In some cases you might find that it becomes completely unusable. Of course, you can always try it, and see how fast it is for you. 

Using XPath Wisely |
---- |
Avoid brittle selectors by replying on unique node attributes. |
<code>//android.widget.TextView[@text="Unique text"]</code> |
<code>//android.widget.Layout[@content-desc="Unique layout"]/android.widget.TextView[1]</code>  |
<code>//ancestor::*[*[@text="foo"]][@text="bar"]</code>  |

Assuming the performance issues do not affect your case, how can you be a wise user of the XPath locator strategy? 

The main thing is to avoid brittle selectors. You can do this by making sure to restrict your XPath query by making use of any unique information. For example, if I know that the node I want has a unique attribute, I should make sure to specify that in the XPath, with the query shown below. I'm guaranteed to find only the element which contains the unique text.

    //android.widget.TextView[@text="Unique text"]

I can also use unique information about a node's ancestor. Here's an example where I'm trying to find a TextView.

    //android.widget.Layout[@content-desc="Unique layout"]/android.widget.TextView[1]

The text view itself has no unique properties, but I know that its parent layout has a content description, which is unique. So I can specify that in the XPath query to make sure that I'm only looking at the first TextView of the unique layout I know it will fall under. 

We can also query the other direction, from knowledge of unique information about a descendant. 

    //ancestor::*[*[@text="foo"]][@text="bar"]

In this final example, I'm looking for an element which has the text "bar", but let's imagine that there are lots of elements that have this text in my app. In this case, I know the element I want is an ancestor of another element, which has the text "foo". I can use this query to restrict my search for elements containing "bar" to only those which are ancestors of elements containing "foo". We can imagine that would restrict my query to only the element I care about. In general, XPath is not something to be afraid of, but it can have *test maintainability as well as test performance consequences if you're not careful*. But now you know everything you need to know to use XPath wisely. So fear not, and onward to your test writing.
