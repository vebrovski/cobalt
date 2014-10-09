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

For now I won't be covering other parameters since they are pretty straightforward.

Once you set up your Comments Parameters users will be able to add comments under your Content-T record.


### Comments rating (reviews)

1. Go edit your type Comment-T.
2. Under *General Parameters -> Templates -> Rating* select your rating template. This is used for clicking icons when you submit your rating (ex. 1-5). You can see how they look in administrator under Cobalt -> Templates -> Ratings. You can also copy template or make your own. Ratings template files are in `components/com_cobalt/views/rating_tmpls`.
3. Stay on the same parameters tab and see parameters under *Rating*. If you are doing reviews choose *Only article author* under *Who can rate* because you don't want your Content-T author to review his own record.
4. Stay on the same tab. If you want to use multiple rating options set *Yes* in *Enable multiple ratings* and write down options under *Multiple rating options*. Write down one option on each line. Multiple rating is useful if you want to rate for ex. restaurant service and you want your users to be able to rate food, service, value, atmosphere...

Now let's see how we will display rating, total rating and votes. We can use:
* `$item->votes` for number of votes,
* `$item->votes_result` for rating result,
* `$rating['num']` for number of votes,
* `$rating['total']` for rating result.

The difference between 1st two and 2nd two is in additional queries. `$rating` method adds one additional query which is OK for full record view but not so for list view.
The difference may also exists in case you have approve system for reviews. `$rating['total']` contains total of all published articles. `$item->votes_result` contain all articles including unpublished.

So in my experience for proper rating all accross site you should use `$rating`. Let's se how to do this.

In Cobalt documentation we have this API `$rating = CobaltApi::renderRating($type_id, $section_id, $condition);` where `$type_id` is ID of content type (Comment-T), `$section_id` is ID of section (Comment-S) and `$condition` are simply conditions for showing rating. I think that there should be at least one condition applied and that's `r.published = 1`. This will take into account only published ratings. `r` stands for `js_res_records` table.

Rating will be shown as number from 0-100. You can always change this with php. 
Ex. `<?php echo round($rating['total']/10, 1);?>` divides total rating by 10 and rounds up number by 0,1. 

### Reply to comments
