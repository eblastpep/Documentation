# CSS

In building emails, knowledge of how to properly deal with CSS is key. While CSS in email tends to be pretty straightforward in that you’re dealing with the same syntax and code that you do in traditional web design, there are notable differences that sometimes run counter to standards-based web design wisdom.

## Stick With the Basics

CSS3 is still a far-off dream in the email world. While some modern clients support a small handful of CSS3 properties, the slow adoption of these newer, more capable email clients forces email design to stay in the past. For that reason, the use of [the CSS2.1 specification](http://www.w3.org/TR/CSS2/) is necessary, though there are some email clients that don’t fully support even this older spec.

## Inlined Styles

CSS **must** be inlined within your HTML document. The [major webmail clients](https://templates.mailchimp.com/concepts/email-clients) don’t support the  element, meaning that external stylesheets aren’t usable. Leaving CSS styles in your email’s  isn’t an option either - Gmail strips the entire element from the HTML when an email is viewed either in its web or mobile clients, leaving an unstyled mess:

The simpler an email’s built and styled, the better the end result will be. There are even some bits of CSS that should be outright ignored, because support for these properties tend to be extremely spotty. You can [turn to the Email Client CSS Support matrix](https://templates.mailchimp.com/resources/email-client-css-support) for guidance of what CSS works where.

## [Client-specific CSS Styles](https://templates.mailchimp.com/development/css/client-specific-styles/)

Some email clients require an extra push in order to get them to behave the way we need. For the most part, overriding those pesky default style rules is a simple proposition, requiring extra style declarations and a **!important** or two. There are instances where client styles can’t be overridden at all, or only with hacky work-arounds. We’ll detail here, client by client, the things you’ll need to be aware of.

### Outlook.com / Hotmail

#### .ExternalClass Class Override

When an email is pulled into Outlook.com / Hotmail, any style rules present in the email are appended with **.ExternalClass**. Normalizing a few of these can help create a baseline for you to work from.

```text
.ExternalClass{
    width:100%;
}

.ExternalClass,
.ExternalClass p,
.ExternalClass span,
.ExternalClass font,
.ExternalClass td,
.ExternalClass div{
    line-height: 100%;
}
```

#### `<hX>` Color Override

Outlook.com / Hotmail sets its own \(gross green\) color on heading elements lower in level than an `<h1>` element. This means you need to account for headings `<h2>` throughh `<h6>`. Fortunately, it’s as easy as applying a **!important** declaration to the heading’s **color** property:

```text
`<h1>`{
    color:#0066CC !important;
}
```

### Outlook 2007 / 2010 / 2013

It s possible, in Outlook, to trigger the appearance of a fairly prominent “View this email in your browser” bar within the application, allowing you to drive people to view your email in a browser which will render it in a much better way than Outlook ever can.

```text
#outlook a{
    padding:0;
}
```

#### Element Spacing

Outlook can sometimes add a bit of spacing on the left and right side of a  element that can cause some layout-related headaches. By using the vendor-specific **mso-table-lspace** and **mso-table-rspace** CSS properties, you can be rid of those spaces and continue on to tackle the million other problems caused by Outlook.

```text
table{
    mso-table-lspace:0pt;
    mso-table-rspace:0pt;
}
```

#### Image Resizing

Using width or height tags to resize images in your markup can create a problem in Internet Explorer browsers. If your reader is viewing an email in-browser, and that email happens to have [fluid images](https://templates.mailchimp.com/development/css/responsive-email/fluid-images) in it, they’ll look pretty ugly as they resize. Using **-ms-interpolation-mode:bicubic;** ensures that your images look a little better.

### OSX / iOS

#### WebKit Text Size Adjustment

WebKit looks for any text that happens to be sized smaller than 13px and increases it to that number, which can sometimes cause design issues in places intended for small text. Setting **-webkit-text-size-adjust** to **none** will prevent iOS platforms from resizing the text, but this method also prevents OSX applications like Safari from bumping the text size up - something that can cause issues for people who need the text size to be large. Setting **-webkit-text-size-adjust** to **100%** seems to be the best of both worlds.

```text
body{
    -webkit-text-size-adjust:100%;
}
```

#### Phone Number, Address, & Date Link Colors

When iOS detects a phone number, address, or calendar date, it oh-so-helpfully sets those items as links to make it easier to immediately call, map, or add an appointment within other apps. The trouble is the that link colors are the standard ‘internet blue’, or **\#0000FF**. This color can be difficult to read on dark backgrounds, to say nothing of it not matching the style of your email’s design. Thankfully, there’s a workaround.

First, for the phone number. In the  of your email, add this iOS-specific  tag:

```text
<meta name="format-detection" content="telephone=no">
```

Using that, iOS will no longer auto-detect and style phone numbers \([Apple also lists its other tags here](http://developer.apple.com/library/safari/#documentation/AppleApplications/Reference/SafariHTMLRef/Articles/MetaTags.html)\). You should, however, provide your own way to call from an email. You can do that by wrapping a phone number in a line and setting the **href** attribute with a **tel** value:

```text
<a href="tel:1-800-555-5555">1-808-555-5555</a>
```

You can now style the link as you see fit. Addresses and dates work a little differently, and the solution is a little hacky, but that’s okay; it’s par for the course in HTML email. In order to override the blue link color, you wrap the links in an inline element like an , then you apply your own color:

```text
Visit EmailCo. at  href="#" style="color:#000000; text-decoration:none;">123 Atlantic Ave. • Atlanta, GA 30318 USA
```

Be aware that, regardless of the fact that you changed the link colors, the iOS functionality isn’t disabled on any of these items. An errant tap of a date or address will still open up calendar and map applications, so it’s a good idea to make these links a little different than your other standard links, so folks avoid confusion or frustration.

### Windows Mobile

#### Windows Mobile Text Size Adjustment

Much like in OSX and iOS, small text is also resized on Windows Mobile. The same vendor-prefix-based CSS property is used here, just with the **ms** vendor prefix instead of the **webkit** one.

```text
body{
    -ms-text-size-adjust:100%;
}
```

## Reset Styles

Just as in traditional web development, it’s a good idea to provide some reset CSS to the emails you build. For the same reason it’s done when building a site, adding a reset to an email helps to normalize how the code gets rendered across multiple email clients. The email templates provided by this reference rely on the following reset:

```text
<style type="text/css">
    /* /\/\/\/\/\/\/\/\/ RESET STYLES /\/\/\/\/\/\/\/\/ */
    body{
        margin:0;
        padding:0;
    }

    img{
        border:0 none;
        height:auto;
        line-height:100%;
        outline:none;
        text-decoration:none;
    }

    a img{
        border:0 none;
    }

    .imageFix{
        display:block;
    }

    table, td{
        border-collapse:collapse;
    }

    #bodyTable{
        height:100% !important;
        margin:0;
        padding:0;
        width:100% !important;
    }
</style>
```

Here’s a breakdown, element by element, of what the reset styles are actually doing:

### body Element Reset

Having a normalized browser baseline to build from ensures more consistent code to be written and, in an email, that starts with resetting the  element. It’s a simple reset; only **margin** and **padding** are targeted.

```text
<style type="text/css">
    body{
        margin:0;
        padding:0;
    }
```

### img Element Reset

In order to make sure the images placed in an email are free of any browser- and email client-based quirks, we have to apply some styles to normalize how they’re rendered.

Some email clients add space below images by default, which is problematic if you’re tiling images. Attach the **.imageFix** class to remove the space. Be aware that, by setting images to block-level elements, you can’t align them without resorting to the **float** or **position** CSS properties, which aren’t widely supported:

```text
img{
        border:0 none;
        height:auto;
        line-height:100%;
        outline:none;
        text-decoration:none;
    }

    a img{
        border:0 none;
    }

    .imageFix{
        display:block;
    }
```

### table and td Element Reset

In a few email clients,  and  elements are sometimes given some extra spacing which can be frustrating in designs that require some measure of pixel-perfect precision. Using **border-collapse** removes that spacing. If you’re using **border-radius** on any element, make sure to set **border-collapse:separate;**, otherwise those rounded corners won’t show up.

```text
    table, td{
        border-collapse:collapse;
    }
```

### bodyTable Reset

Since Gmail strips the  element from incoming emails, it’s necessary to clone it in order to provide a fallback. Generally, a  is used, and some styles must be applied to it in order to have it act like a  element would:

```text

```

## [Outlook Conditional CSS](https://templates.mailchimp.com/development/css/outlook-conditional-css/)

It’s no secret that Microsoft’s Outlook desktop email client is the bane of every email designer’s existence. With its terrible CSS support, getting an email to look as good in Outlook as it may in Yahoo or Gmail can be difficult at the best of times.

But why should your email look exactly the same in every client? The pursuit of pixel-perfect design can often leave you spinning your wheels trying to fix tiny bugs and discrepancies that no one will notice. Sometimes, it’s okay to take the path of least resistance; Dan Cederholm espouses the same thought in regard to websites.

With the multitude of weird rendering issues Outlook introduces into emails, it makes more and more sense to provide an Outlook-specific stylesheet, akin to what we do for mobile emails. Using Microsoft’s conditional comments, you can do just that. Here’s all you need, and it can be placed after your main CSS:

```text
body{
    <!--[if gte mso 9]>
    <style type="text/css">
    /* Your Outlook-specific CSS goes here. */
    </style>
<![endif]-->
}
```

It’s the same thing you’d use to target Internet Explorer 7 when building a website, except it targets Microsoft Office, or ‘mso’. In the example above, ‘gte’ is added to have the comment apply only to versions of Microsoft Office greater than or equal to 9. You can also target lesser, or older, versions by using ‘lt’. Using ‘gt’ and ‘lte’ will target versions greater than, or less than or equal to, respectively.

### Outlook Version Numbers

Using Microsoft Office version numbers allows you to target specific Outlook clients. In the example above, ‘mso 9’ is Office 2000, which means you’re targeting Outlook 2000. Other version numbers allow you to target other clients, meaning you can build stylesheets tailored to each version of Outlook.

Outlook 2000 - Version 9 Outlook 2002 - Version 10 Outlook 2003 - Version 11 Outlook 2007 - Version 12 Outlook 2010 - Version 14 Outlook 2013 - Version 15 Because Outlook 2003 - 2003 was driven by the IE6 rendering engine, those client versions are relatively stable. From Outlook 2007 and on, the rendering engine was changed to Word, making Outlook incredibly problematic for email. You can leverage this conditional CSS to squash bugs and greatly reduce your email development-related headaches.

## The Outlook 2007-2010 Page Break Problem

One of the best reasons to use this conditional CSS is to mitigate various rendering issues in Outlook, including its page break issue. Outlook 2007, or more specifically the Word engine, has a document length limit of 22 inches, or around 1800 pixels. When a document - an email, in this case - hits that length, Outlook inserts a page break to aid with document printing. This page break can have serious effects on an email:

By using conditional styles and making your emails responsive to Outlook, you can mitigate or even avoid the page break problem and other rendering issues entirely.

In our own testing, we’ve found that this bug no longer presents itself in Outlook 2013. Progress

