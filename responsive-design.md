# Responsive Design

Responsive web design, a term [first coined by Ethan Marcotte](http://alistapart.com/article/responsive-web-design), is the practice of crafting websites in a way that they are usable regardless of which device is used to access them. The responsive web is largely reliant on [media queries](http://www.w3.org/TR/css3-mediaqueries/) to drive that adaptation. More recently, this approach has been brought to the world of HTML email.

A media query follows a pretty simple structure. For the purposes of email, the media query’s styles are nested within the emails **&lt;style&gt;** tag:

```text
<style type="text/css">
    .standardCSS{
        color:#505050;
        font-size:15px;
    }

    @media only screen and (max-width:480px){
        .mediaQueryCSS{
            color:#CCCCCC;
            font-size:20px;
        }
    }
</style>
```

Basically a stylesheet within a stylesheet, media queries are built from a few parts:

```text
@media only screen and (max-width:480px){
}
```

First, they’re always opened with the **@media** at-rule. Next comes a keyword. In this case, ‘**only**’, which restricts the display of the media query styles to the specified media type. After that, the ‘**media type**’ is set. The most commonly used media types are **screen** and **print**, providing different style rules for displays and printers, respectively. Another keyword, ‘**and**’, follows, and, finally, the ‘**media feature**’, which is the meat of the media query.

There are [many different media features](http://www.w3.org/TR/css3-mediaqueries/#media1) available, but we’re really only concerned with using two of them:

* max-width
* max-device-width

The difference between them is how they calculate the maximum width that will trigger the media query. The feature **max-width** is measured against the available space of a browser, while **max-device-width** looks at the size of the device’s screen. Essentially, max-width covers everything and max-device-width covers small displays.

We want to cover everything. In this case, the media feature is used to target an area that measures 480 pixels in width or smaller. 480px is the standard width of a mobile phone’s screen in landscape orientation, and a good standard breakpoint to use in your code.

### Media Queries and Inline Styles

As [noted in the CSS overview](https://templates.mailchimp.com/development/css/), it’s necessary to inline CSS styles, either by hand [or automatically](https://templates.mailchimp.com/resources/inline-css) before an email is sent. Since media query styles work on a trigger and are not default styles, it doesn’t make sense to inline any of it. So, the email’s normal CSS needs to be inlined and the media query CSS needs to override those styles once its triggered.

Because inline styles have the highest specificity in the cascade, it’s necessary for every media query style rule you write needs to be marked with a **!important** declaration:

```text
<style type="text/css">
    .standardCSS{
        color:#505050;
        font-size:15px;
    }

    @media only screen and (max-width:480px){
        .mediaQueryCSS{
            color:#CCCCCC !important;
            font-size:20px !important;
        }
    }
</style>
```

## Increasing Text Size

Using media queries to increase an email’s text size when it’s viewed on a mobile device is one of the easiest things you can do to make an email better for readers. Here’s the standard CSS for our email’s body copy:

```text
<style type="text/css">
  .bodyContent{
    color:#505050;
    font-family:Helvetica, Arial, sans-serif;
    font-size:14px;
    line-height:125%;
  }
</style>
```

While a 14px font size is fine on a desktop, it can often be a struggle to read anything of that size on a small screen. We recommend a font size of at least 16px for main copy. Getting that to happen is a pretty simple proposition, since we’re only concerned with the font size:

```text
<style type="text/css">
  @media only screen and (max-width: 480px){
    .bodyContent{font-size:16px !important;}
  }
</style>
```

When the media query is triggered on screens less than 480px wide, the text size is automatically bumped up to a readable size:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/text-resize.png)

## Responsive Column Layouts

It can be difficult to read a multi-column email on a small screen; having to scroll horizontally is kind of a pain. Reading on small displays tends to work better if it’s done in a linear fashion, from top-to-bottom. Using media queries we can switch a multi-column layout to a single-column one, making it easier to read an email. There are two methods that will be covered here, each with its pros and cons.

### Method 1 - Block-level  Elements

The first method uses the same column structure covered in the HTML section’s [Column Layouts](https://templates.mailchimp.com/development/html/column-layouts) page. Here’s that code:

```text
<table border="0" cellpadding="0" cellspacing="0" width="600" id="templateColumns">
    <tr>
        <td align="center" valign="top" width="50%" class="templateColumnContainer">
            <table border="0" cellpadding="10" cellspacing="0" width="100%">
                <tr>
                    <td class="leftColumnContent">
                        <img src="http://placekitten.com/g/480/300" width="280" style="max-width:280px;" class="columnImage" />
                    </td>
                </tr>
                <tr>
                    <td valign="top" class="leftColumnContent">
                        <h1>Left Column</h1>
                        Lorem ipsum dolor sit amet.
                    </td>
                </tr>
            </table>
        </td>
        <td align="center" valign="top" width="50%" class="templateColumnContainer">
            <table border="0" cellpadding="10" cellspacing="0" width="100%">
                <tr>
                    <td class="rightColumnContent">
                        <img src="http://placekitten.com/g/480/300" width="280" style="max-width:280px;" class="columnImage" />
                    </td>
                </tr>
                <tr>
                    <td valign="top" class="rightColumnContent">
                        <h1>Right Column</h1>
                        Lorem ipsum dolor sit amet.
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

The default styling for the columns isn’t really a concern here, so let’s skip straight to the media query styles:

```text
<style type="text/css">
    @media only screen and (max-width: 480px){
        #templateColumns{
            width:100% !important;
        }

        .templateColumnContainer{
            display:block !important;
            width:100% !important;
        }

        .columnImage{
            height:auto !important;
            max-width:480px !important;
            width:100% !important;
        }

        .leftColumnContent{
            font-size:16px !important;
            line-height:125% !important;
        }

        .rightColumnContent{
            font-size:16px !important;
            line-height:125% !important;
        }
    }
</style>
```

We start off the media query stylesheet by setting the **templateColumns** table width to 100%, so it’ll adapt to different screen sizes or orientations. The important bit in how this layout switching works comes next: by setting each **templateColumnContainer**  to block-level elements, and reinforcing that with the 100% width declaration, the right column snaps under the left, and we have a single-column view. After that, the image gets its fluid styling and the text gets a size bump.

As we mentioned, each method has its pros and cons. This method is the most stable; by virtue of using standard, tried-and-tested coding conventions, the columns work in every client in desktop view. There are a couple of downsides, however. First is the fact that setting a `<td>` as a block-level element runs counter to how they’re actually supposed to be used. Second, you can’t swap the order of the columns or do any other fancy layout-shifting stuff without resorting to CSS **float** or **position** - properties that don’t enjoy wide or consistent support across email clients.

So, if your aim is stability over flexibility, this is the way to go.

### Method 2 - Aligned table Elements

Method 2 is the opposite of the first; its strength is flexibility, but it’s not very stable. The columns are coded a little differently:

```text
<table border="0" cellpadding="0" cellspacing="0" width="600" id="templateColumns">
    <tr>
        <td align="center" valign="top">
            <table align="left" border="0" cellpadding="10" cellspacing="0" width="300" class="templateColumnContainer">
                <tr>
                    <td class="leftColumnContent">
                        <img src="http://placekitten.com/g/480/300" width="280" style="max-width:280px;" class="columnImage" />
                    </td>
                </tr>
                <tr>
                    <td valign="top" class="leftColumnContent">
                        <h1>Left Column</h1>
                        Lorem ipsum dolor sit amet.
                    </td>
                </tr>
            </table>
            <table align="right" border="0" cellpadding="10" cellspacing="0" width="300" class="templateColumnContainer">
                <tr>
                    <td class="rightColumnContent">
                        <img src="http://placekitten.com/g/480/300" width="280" style="max-width:280px;" class="columnImage" />
                    </td>
                </tr>
                <tr>
                    <td valign="top" class="rightColumnContent">
                        <h1>Right Column</h1>
                        Lorem ipsum dolor sit amet.
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

Each column `<table>` sits within just one `<td>` this time, and they each get a fixed width of 300px. The most important difference is the presence of the **align** attribute on each table; this attribute mimics the functionality of CSS **float**, but it’s actually well-supported across email clients since its HTML-spec.

In the HTML, we’ve moved the class name **templateColumnContainer** from the `<td>` elements seen in the first example to both of the aligned `<table>` elements that make up the left and right columns. Because each `<table>` is essentially floated, they automatically wrap when the media query is triggered. There’s only one change required in the media query style for **templateColumnContainer**; we’re now targeting a `<table>` instead of a `<td>` element:

```text
<style type="text/css">
    @media only screen and (max-width: 480px){
        .templateColumnContainer{
            display:block !important;
            width:100% !important;
        }
    }
</style>
```

Setting the width of each column to 100% allows them to fill the available screen space, just as in the first example. So, what’s bad about this method? Instability. Because the columns are essentially floated, they tend to wrap under one another if there’s any layout wonkiness. You’re likely to run into this problem if the column `<table>` elements “touch” each other, like placing two 300px-wide columns next to each other in a 600px space. It’s all dependent on how an email client renders tables or calculates widths. Making the columns a little narrower than total width of the space they live in is sometimes a suitable work-around, but it’s not 100% fool-proof.

In spite of those issues, the flexibility inherent to this method and how it allows for layout switching, which you can see covered in [Layout Manipulation](https://templates.mailchimp.com/development/responsive-email/layout-manipulation), is the best reason to use it.

## Usable Link Groups

There are places in an email where a group of several links are clustered together. The most common example can be found in the footer of an email, where utility links like ‘view in browser’, ‘unsubscribe’, and ‘follow us on twitter’ often appear:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/usable-link-group-before.png)While these link groups don’t present a problem when an email is viewed on a desktop, they can cause issues on small screens; it’s often difficult to tap just one link in a group when you’re using a finger. This clearly presents a usability issue, not to mention the fact that it’s frustrating for users.

Luckily, with the use of media query mobile styles, we can provide an alternative. First, a look at the default html:

```text
<table border="0" cellpadding="0" cellspacing="0" width="100%" id="emailFooter">
    <tr>
        <td align="center" valign="top" class="footerContent" style="padding-bottom:15px;">
            © 2013 EmailCo, All Rights Reserved.
            <br />
            123 Atlantic Ave. • Atlanta, GA 30318 USA
        </td>
    </tr>
    <tr>
        <td align="center" valign="top">
            <table border="0" cellpadding="5" cellspacing="0" id="utilityLink">
                <tr>
                    <td valign="top" class="utilityLinkContent">
                        <a href="..." target="_blank">View in Browser</a>
                    </td>
                    <td valign="top" class="utilityLinkContent">
                        <a href="..." target="_blank">Terms of Use</a>
                    </td>
                    <td valign="top" class="utilityLinkContent">
                        <a href="..." target="_blank">Privacy Policy</a>
                    </td>
                    <td valign="top" class="utilityLinkContent">
                        <a href="..." target="_blank">Unsubscribe</a>
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

You’ll notice that the links themselves are in a separate, nested table; odd, we know, but there’s a good reason for it, which we’ll get to. First, here’s the default CSS:

```text
<style type="text/css">
    .footerContent, .utilityLinkContent{
        color:#606060;
        font-family:Helvetica, Arial, sans-serif;
        font-size:13px;
        line-height:125%;
    }

    .footerContent a, .utilityLinkContent a{
        color:#6DC6DD;
    }
</style>
```

Everything is styled nicely, and using the **cellpadding** attribute on the nested table gives us a nice bit of spacing for the links. Great for the desktop, but the ideal solution for small screens is to place every link on its own line, and give them enough separation that they’re easy to tap:

```text
<style type="text/css">
    @media only screen and (max-width: 480px){
        #utilityLink{
            max-width:600px !important;
            width:100% !important;
        }

    .utilityLinkContent{
            background-color:#E1E1E1 !important;
            border-bottom:10px solid #FFFFFF;
            display:block !important;
            font-size:15px !important;
            padding:15px 0 !important;
            text-align:center !important;
            width:100% !important;
        }

        .utilityLinkContent a{
            color:#606060 !important;
            display:block !important;
            text-decoration:none !important;
        }
    }
</style>
```

By giving them a background color, we’re essentially turning each of those link-containing `<td>` elements into buttons. We’re also bumping up the text size, setting the links to block-level elements, and changing some of the padding. By adding **border-bottom** to each `<td>`, and matching the color to the email background, we give the link buttons a little separation, as well. We end up with this result:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/usable-link-group-after.png)Each link is now easy to tap with a finger, and everyone’s happy.

## Adaptive Buttons

It can be helpful, in certain situations, to have an email’s button span the full width of the screen on mobile devices. While right-handed users are more comfortable tapping the left side of a phone screen with their thumbs, left-handed users are more comfortable tapping the right. In the case of a strong call-to-action, like a button, making it easy for anyone press can be beneficial.

Here’s a standard HTML button, and how it appears when an email is viewed on a desktop:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/button-resize-before.png)

```text
<table border="0" cellpadding="0" cellspacing="0" class="emailButton" style="border-radius:3px; background-color:#6DC6DD;">
  <tr>
    <td align="center" valign="middle" class="emailButtonContent" style="padding-top:15px; padding-right:30px; padding-bottom:15px; padding-left:30px;">
      <a href="..." target="_blank" style="color:#FFFFFF; font-family:Helvetica, Arial, sans-serif; font-size:16px; font-weight:bold; text-decoration:none;">Buy Now</a>
    </td>
  </tr>
</table>
```

Primarily, we want to change the button’s width on small screens, but it’s also a good idea to increase the text size and change the link to a block-level element:

```text
<style type="text/css">
  @media only screen and (max-width: 480px){
    .emailButton{
      max-width:600px !important;
      width:100% !important;
    }

    .emailButton a{
      display:block !important;
      font-size:18px !important;
    }
  }
</style>
```

The code above results in a more usable and readable button:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/button-resize-after.png)

## Fluid Images

Having your images resize on small screens is one of the most important benefits of using media queries, especially where email is concerned. Since one of the most common media query trigger sizes for emails is 480px \(the width of most phones in landscape orientation\), images wider than that 480px would result in some horizontal scrolling, which should generally be avoided.

Making an image fluid, however, solves that problem. By virtue of being fluid, the image is also adaptable to different media query trigger sizes. Here’s our example image and the code behind it:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/image-resize-before.png)

```text
<img src="" alt="" height="" width="" class="emailImage" />
```

Pretty simple stuff. Now onward to the media query CSS:

```text
<style type="text/css">
    @media only screen and (max-width: 480px){
        .emailImage{
            height:auto !important;
            max-width:600px !important;
            width: 100% !important;
        }
    }
</style>
```

When the email is viewed on a screen that is 480px and smaller, the image has its height and width overridden, it also gets a max-width designated so that the image doesn’t look blown out proportion. As a rule of thumb, the max-width should be the same as the image’s original width. What we end up with, once the media query is triggered, is an image that fits on a small display:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/image-resize.png)The image is fluid, so it will fill its available space regardless of whether the screen orientation is portrait or landscape, and readers don’t have to deal with horizontal scrolling.

## Layout Manipulation

As we mentioned in [Responsive Multi-column Layouts](https://templates.mailchimp.com/development/responsive-email/responsive-column-layouts), using aligned `<table>` elements as columns allows for a lot of flexibility in terms of mobile layout. The clearest example of that is an email with a left sidebar and right main content area:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/email-with-sidebar-desktop.png)Layout switching in cases like this is very useful; generally, sidebars are for content that’s secondary in importance to the main body of the email. In the desktop view, the fact that the sidebar is to the left of the main content doesn’t really affect how you might read the email. On a small display, however, the sidebar would end up above the body, which doesn’t make much semantic sense:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/email-with-sidebar-before.png)We can use the aligned `<table>` method to swap the order of the sidebar and body section and thus maintain proper semantic order when the email is viewed on a small display. The HTML is very similar to other responsive column layouts. The major differences are in the markup, where the sidebar and body are placed in a semantic order, and the **align** attribute directions on each table are opposite of their desktop-view positions:

```text
<table border="0" cellpadding="0" cellspacing="0" width="600" id="templateContainer">
    <tr>
        <td align="left" valign="top">
            <table align="right" border="0" cellpadding="10" cellspacing="0" width="400" id="templateBody">
                <tr>
                    <td class="bodyContent">
                        <img src="http://placekitten.com/g/480/300" style="max-width:380px;" class="bodyImage" />
                    </td>
                </tr>
                <tr>
                    <td valign="top" class="bodyContent">
                        <h1>Body</h1>
                        Lorem ipsum dolor sit amet.
                    </td>
                </tr>
            </table>
            <table align="left" border="0" cellpadding="10" cellspacing="0" width="200" id="templateSidebar">
                <tr>
                    <td valign="top" class="sidebarContent">
                        <h2>Sidebar</h2>
                        Lorem ipsum dolor sit amet.
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

The media query CSS controls the width of each content area, along with the content itself:

```text
<style type="text/css">
    @media only screen and (max-width: 480px){
        #templateSidebar,
        #templateBody{
            display:block !important;
            width:100% !important;
        }

        .sidebarContent{
            font-size:16px !important;
            line-height:125% !important;
        }

        .bodyImage{
            height:auto !important;
            max-width:480px !important;
            width:100% !important;
        }

        .bodyContent{
            font-size:18px !important;
            line-height:125% !important;
        }
    }
</style>
```

With the body placed on the left, but aligned to the right and the sidebar placed on the right and aligned to the left, the sections snap into place once the media query triggers:

![](https://cdn-images.mailchimp.com/email-reference/development/media-queries/email-with-sidebar-mobile.png)\#\# The Outlook Problem

There’s a major problem with using this aligned table method, specifically having to do with [Outlook’s 22 inch height limit on documents](http://support.microsoft.com/kb/95109). When a document reaches this limit, around 1800 pixels in length, Outlook 2007 & 2010 oh-so-helpfully inserts a page break to aid with printing. That page break ends up being inserted _between_ the aligned tables, which causes one to move under the other:

![](https://cdn-images.mailchimp.com/email-reference/development/css/page-break-bug.png)There’s no way to disable this “feature”; you’re relegated to finding work-arounds. We stick to two methods: emails with concise, short content so that they never hit that 22"/1800px mark, or [conditional styling for Outlook](https://templates.mailchimp.com/development/css/outlook-conditional-css/), which can mitigate the issue and make it far less noticeable.

The [captioned image](https://templates.mailchimp.com/development/html/captioned-images) design pattern is ubiquitous in email, so building responsive versions of this pattern is a must. The way these responsive blocks work is mechanically similar to [responsive column layouts](https://templates.mailchimp.com/development/responsive-email/responsive-column-layouts), with some extra bits mixed in.

## Responsive Captioned Images

### Top Caption Image

For images with a top or bottom caption, the code is straightforward. These two patterns are built using one two-row table. For styling flexibility, the caption text is contained within a nested table:

![](https://cdn-images.mailchimp.com/email-reference/resources/email-design-patterns/caption-image-top.jpg)

```text
<table border="0" cellpadding="0" cellspacing="0" width="100%" style="background-color:#E5E5E5; border:1px solid #CCCCCC;">
    <tr>
        <td align="center" valign="top" style="padding-right:20px; padding-bottom:20px; padding-left:20px;">
            <table border="0" cellpadding="0" cellspacing="0" width="100%">
                <tr>
                    <td valign="top" style="color:#404040; font-family:Helvetica; font-size:16px; line-height:125%; text-align:left; padding-top:20px;">
                        <h3>Kitten</h3>
                        <br />
                        A kitten or kitty is a juvenile domesticated cat. A feline litter usually consists of two to five kittens. To survive, kittens need the care of their mother for the first several weeks of their life. Kittens are highly social animals and spend most of their waking hours playing and interacting with available companions.
                    </td>
                </tr>
                <tr>
                    <td valign="top" style="padding-top:20px;">
                        <img src="..." width="520" />
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

### Bottom Caption Image

The bottom caption image uses the same code, with the row order swapped:

![](https://cdn-images.mailchimp.com/email-reference/resources/email-design-patterns/caption-image-bottom.jpg)

```text
<table border="0" cellpadding="0" cellspacing="0" width="100%" style="background-color:#E5E5E5; border:1px solid #CCCCCC;">
    <tr>
        <td align="center" valign="top" style="padding-top:20px; padding-right:20px; padding-left:20px;">
            <table border="0" cellpadding="0" cellspacing="0" width="100%">
                <tr>
                    <td valign="top" style="padding-bottom:20px;">
                        <img src="..." width="520" />
                    </td>
                </tr>
                <tr>
                    <td valign="top" style="color:#404040; font-family:Helvetica; font-size:16px; line-height:125%; text-align:left; padding-bottom:20px;">
                        <h3>Kitten</h3>
                        <br />
                        A kitten or kitty is a juvenile domesticated cat. A feline litter usually consists of two to five kittens. To survive, kittens need the care of their mother for the first several weeks of their life. Kittens are highly social animals and spend most of their waking hours playing and interacting with available companions.
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

### Responsive Top & Bottom Caption Image

A responsive version of the top and bottom caption image block is driven primarily by the media query style attached to the image, which needs to be made fluid or adaptive to multiple display sizes.

![](https://cdn-images.mailchimp.com/email-reference/resources/email-design-patterns/caption-image-bottom-responsive.jpg)The code for the block remains the same, save for **class=“flexibleImage”**, which gets added to the image…

```text
<table border="0" cellpadding="0" cellspacing="0" width="100%" style="background-color:#E5E5E5; border:1px solid #CCCCCC;">
    <tr>
        <td align="center" valign="top" style="padding-top:20px; padding-right:20px; padding-left:20px;">
            <table border="0" cellpadding="0" cellspacing="0" width="100%">
                <tr>
                    <td valign="top" style="padding-bottom:20px;">
                        <img src="..." width="520" />
                    </td>
                </tr>
                <tr>
                    <td valign="top" style="color:#404040; font-family:Helvetica; font-size:16px; line-height:125%; text-align:left; padding-bottom:20px;">
                        <h3>Kitten</h3>
                        <br />
                        A kitten or kitty is a juvenile domesticated cat. A feline litter usually consists of two to five kittens. To survive, kittens need the care of their mother for the first several weeks of their life. Kittens are highly social animals and spend most of their waking hours playing and interacting with available companions.
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

…and is referenced in the media query styles that resize the image according to display size. Just two styles are needed; the image’s original full width of 520px is set as its **max-width**, and its **width** is set to 100%, making it fluid:

```text
<style type="text/css">
    @media only screen and (max-width: 480px){
        .flexibleImage{
            max-width:520px !important;
            width 100% !important;
        }
    }
</style>
```

### Right Caption Image

Right and left caption image blocks are slightly more complex in the way they’re constructed. Essentially, there are [two methods](https://templates.mailchimp.com/development/responsive-email/responsive-column-layouts) to building this pattern. Here, we’ll concentrate on the ‘aligned table’ method, since it’s the most flexible. The block is built using two aligned tables, nested within a containing table. One aligned table holds the image, the other holds the text:

![](https://cdn-images.mailchimp.com/email-reference/resources/email-design-patterns/caption-image-right.jpg)

```text
<table border="0" cellpadding="0" cellspacing="0" width="100%" style="background-color:#E5E5E5; border:1px solid #CCCCCC;">
    <tr>
        <td valign="top" style="padding-top:20px; padding-right:20px; padding-left:20px;">
            <table align="left" border="0" cellpadding="0" cellspacing="0" width="200">
                <tr>
                    <td align="center" valign="top" style="padding-bottom:20px;">
                        <img src="..." width="200" />
                    </td>
                </tr>
            </table>
            <table align="right" border="0" cellpadding="0" cellspacing="0" width="300">
                <tr>
                    <td valign="top" style="color:#404040; font-family:Helvetica; font-size:16px; line-height:125%; text-align:left; padding-bottom:20px;">
                        <h3>Kitten</h3>
                        <br />
                        A kitten or kitty is a juvenile domesticated cat. Kittens are highly social animals and spend most of their waking hours playing and interacting with available companions.
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

### Left Caption Image

![](https://cdn-images.mailchimp.com/email-reference/resources/email-design-patterns/caption-image-left.jpg)For an image block with a caption on the left side, the only change that needs to be made is to the values in each tables align attribute. Simply swap the alignment direction, and you get the desired layout change:

```text
<table border="0" cellpadding="0" cellspacing="0" width="100%" style="background-color:#E5E5E5; border:1px solid #CCCCCC;">
    <tr>
        <td valign="top" style="padding-top:20px; padding-right:20px; padding-left:20px;">
            <table align="right" border="0" cellpadding="0" cellspacing="0" width="200">
                <tr>
                    <td align="center" valign="top" style="padding-bottom:20px;">
                        <img src="..." width="200" />
                    </td>
                </tr>
            </table>
            <table align="left" border="0" cellpadding="0" cellspacing="0" width="300">
                <tr>
                    <td valign="top" style="color:#404040; font-family:Helvetica; font-size:16px; line-height:125%; text-align:left; padding-bottom:20px;">
                        <h3>Kitten</h3>
                        <br />
                        A kitten or kitty is a juvenile domesticated cat. Kittens are highly social animals and spend most of their waking hours playing and interacting with available companions.
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

Here, the reason we only change the value of the align attribute is so that we follow the semantic standard laid out for [layout manipulation](https://templates.mailchimp.com/development/responsive-email/layout-manipulation). On small displays, when the media query kicks in and the responsive version of the block is rendered, our content maintains an order that makes sense.

### Responsive Right & Left Caption Image

A responsive version of the right & left caption image blocks requires a slightly more complex media query ruleset. Not only does the image have to be made fluid, the side-by-side layout must be swapped to a vertical stack as well:

![](https://cdn-images.mailchimp.com/email-reference/resources/email-design-patterns/caption-image-responsive.jpg)Like with the top & bottom caption image blocks, **class=“flexibleImage”** is added to the image element. In addition, **class=“flexibleContainer”** is added to each of the aligned tables:

```text
<table border="0" cellpadding="0" cellspacing="0" width="100%" style="background-color:#E5E5E5; border:1px solid #CCCCCC;">
    <tr>
        <td valign="top" style="padding-top:20px; padding-right:20px; padding-left:20px;">
            <table align="left" border="0" cellpadding="0" cellspacing="0" width="200" class="flexibleContainer">
                <tr>
                    <td align="center" valign="top" class="imageContent">
                        <img src="..." width="200" class="flexibleImage" />
                    </td>
                </tr>
            </table>
            <table align="right" border="0" cellpadding="0" cellspacing="0" width="300" class="flexibleContainer">
                <tr>
                    <td valign="top" style="color:#404040; font-family:Helvetica; font-size:16px; line-height:125%; text-align:left; padding-bottom:20px;">
                        <h3>Kitten</h3>
                        <br />
                        A kitten or kitty is a juvenile domesticated cat. Kittens are highly social animals and spend most of their waking hours playing and interacting with available companions.
                    </td>
                </tr>
            </table>
        </td>
    </tr>
</table>
```

We can then manipulate the tables using media query styles:

```text
<style type="text/css">
    @media only screen and (max-width: 480px){
        .flexibleImage{
            max-width:520px !important;
            width 100% !important;
        }

        .flexibleContainer{
            display:block !important;
            width:100% !important;
        }
    }
</style>
```

