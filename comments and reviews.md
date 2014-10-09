I merged here several discussions about comments and reviews for [Cobalt](http://www.mintjoomla.com/joomla-components/cobalt.html) extension from [MintJoomla](http://www.mintjoomla.com).

Discussions relevant to this documentation are:
- [Comments and reviews](http://www.mintjoomla.com/support/community-forum/user-item/1089-klox7/48-cobalt-8/3140-comments-and-reviews.html)
- [Rating issues with mutiple rating](http://www.mintjoomla.com/support/community-forum/user-item/1089-klox7/48-cobalt-8/4707-rating-issues-with-mutiple-rating.html)
- [Different total rating result in multirating](http://www.mintjoomla.com/support/community-forum/user-item/1089-klox7/48-cobalt-8/3269-different-total-rating-result-in-multirating.html)
- [Display comments on records listing page](http://www.mintjoomla.com/support/community-forum/user-item/3819-btech/48-cobalt-8/3630-display-comments-on-records-listing-page.html)

Please also read [API reference](http://docs.mintjoomla.com/en/cobalt/api-reference/) on [official MintJoomla Documentation site](http://docs.mintjoomla.com/).

I will write here just how to add comments with reviews and different tricks and tips regarding this. I will not write other topics like creating content types, sections, fields etc.

## Cobalt - Types as comments

### Enable comments

1. Set up type (with fields) and section for your content. We'll call them **Content-T** and **Content-S**.
2. Set up another type (with fields) and section for your comments. We'll call them **Comment-T** and **Comment-S**. You can use Content-S subcategory to store comments but this type of setup can be tricky so I think it's best to store it in different section.
3. Go edit your Content-T and click on *Comments Parameters* tab.
4. In *Comments provider* choose *Cobalt - Types as comments*.
5. *Select section* to store comments (Comment-S), *Content type* (Comment-T) and *Force category* if you want. Purpose of *Force category* is to store comments in section category instead of section root. Remember to check *Submission paramaters* tab in your type (Content-T) and under *Categories Behaviour* you have *Allow root submission*. If you don't have subcategories in your Comment-S than this should be set to Yes.

For now I won't be covering other paramaters since they are pretty straightforward.

Once you set up your Comments Parameters usesr will be able to add comments under your Content-T record.


### Comments rating (reviews)

1. Go edit your Comment-T.

### Reply to comments
