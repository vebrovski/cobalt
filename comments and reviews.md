I merged here several discussions about comments and reviews for [Cobalt](http://www.mintjoomla.com/joomla-components/cobalt.html) extension from [MintJoomla](http://www.mintjoomla.com).

Discussions relevant to this documentation are:
- [Comments and reviews](http://www.mintjoomla.com/support/community-forum/user-item/1089-klox7/48-cobalt-8/3140-comments-and-reviews.html)
- [Rating issues with mutiple rating](http://www.mintjoomla.com/support/community-forum/user-item/1089-klox7/48-cobalt-8/4707-rating-issues-with-mutiple-rating.html)
- [Different total rating result in multirating](http://www.mintjoomla.com/support/community-forum/user-item/1089-klox7/48-cobalt-8/3269-different-total-rating-result-in-multirating.html)
- [Display comments on records listing page](http://www.mintjoomla.com/support/community-forum/user-item/3819-btech/48-cobalt-8/3630-display-comments-on-records-listing-page.html)

Please also read [API reference](http://docs.mintjoomla.com/en/cobalt/api-reference/) on [official MintJoomla Documentation site](http://docs.mintjoomla.com/).

I will write here just how to add comments with reviews and different tricks and tips regarding this. I will not write other topics like creating content types, sections, fields etc.

Thanks to Sergey (developer) for his outstanding support on the forum. Without him I couldn't have written this.

## Cobalt - Types as comments

### Enable comments

1. Set up type (with fields) and section for your content. We'll call them **Content-T** and **Content-S**.
2. Set up another type (with fields) and section for your comments. We'll call them **Comment-T** and **Comment-S**. You can use Content-S subcategory to store comments but this type of setup can be tricky so I think it's best to store it in different section.
3. Go edit your Content-T and click on *Comments Parameters* tab.
4. In *Comments provider* choose *Cobalt - Types as comments*.
5. *Select section* to store comments (Comment-S), *Content type* (Comment-T) and *Force category* if you want. Purpose of *Force category* is to store comments in section category instead of section root. Remember to check *Submission paramaters* tab in your type (Comment-T) and under *Categories Behaviour* you have *Allow root submission*. If you don't have subcategories in your Comment-S than this should be set to Yes.

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
* `$rating['total']` for total rating result.

The difference between 1st two and 2nd two is in additional queries. `$rating` method adds one additional query which is OK for full record view but not so for list view.
The difference may also exists in case you have approve system for reviews. `$rating['total']` contains total of all published articles. `$item->votes_result` contain all articles including unpublished. Also when using `$item->votes_result` and you delete review rating is not recalculated.

So in my experience for proper total rating and total votes all accross site you should use `$rating`. Let's se how to do this.

#### Total rating

In Cobalt documentation we have this API `$rating = CobaltApi::renderRating($type_id, $section_id, $condition);` where `$type_id` is ID of content type (Comment-T), `$section_id` is ID of section (Comment-S) and `$condition` are simply conditions for showing rating. I think that there should be at least one condition applied and that's `r.published = 1`. This will take into account only published ratings. `r` stands for `js_res_records` table.

Another thing you should concider is to add another condition to your ratings and that is `r.parent_id`. This means that only ratings of current article wil be calculated.

**Here are examples for different templates:**

I'll just make up ID's:
* Comment type (Comment-T) ID is 2.
* Comment section (Comment-S) ID is 2.

*Full record view (Content-T)*
```
<?php $rating = CobaltApi::renderRating(2, 2, 'r.published = 1 AND r.parent_id = '.$item->id); ?>
<?php echo $rating['total'];?>
<?php echo $rating['num'];?>
```

*List view (Comment-S) in Full record view (Content-T). In other words template to show list of comments under article.*
```
<?php $rating = CobaltApi::renderRating(2, 2, 'r.published = 1 AND r.parent_id = '.$item->parent_id); ?>
<?php echo $rating['total'];?>
<?php echo $rating['num']; ?>
```

This 2 examples are the same apart from conditions. In 1st example we use `r.parent_id = '.$item->id` because we are in Content-T full record view. `r.parent_id = '.$item->parent_id` because we are in Comment-S list view.

*List view (Content-S). In other words list view of your content records*

```
<?php $rating = CobaltApi::renderRating(13, 8, 'r.published = 1 AND r.parent_id = '.$item->id); ?> 
<?php $rating['total'];?>
```

Rating will be shown as number from 0-100. You can always change this with php. 
Ex. `<?php echo round($rating['total']/10, 1);?>` divides total rating by 10 and rounds up number by 0,1.

#### What about $item->votes_result

Above we were talking about TOTAL RATING (ratings from all reviews of one record). `$item->votes_result` is still useful for displaying result of SINGLE review. So for every comment bellow record or in section view we can use `$item->votes_result`.

Example: `<?php echo $item->votes_result; ?>`

#### Show parent record in review

If you are in your Content-T record you can see reviews under record. But if you go to list view of all comments or in comment full view you don't see which record was reviewed with this comment. You can simply fix this by adding
```
<?php
$parent = ItemsStore::getRecord($item->parent_id);
echo '<a href="'.JRoute::_(Url::record($parent->id)).'">'.$parent->title.'</a>';
?>
```

### Reply to comments rating

This is tricky with core Cobalt since it doesn't have this deep integration. You can hack core if you want. I think `com_cobalt/views/record/tmpl/default_comments.php` is the file you are looking for.

But there is a kind of workaround. I will be making example for 1 reply only (just like JED has).

#### Replies with Cobalt - Type as comment

1. Go edit your Comment-T and click on *Comments Parameters* tab.
2. In *Comments provider* choose *Cobalt - Types as comments*.
3. I think there should be only one reply so you need to enter 1 *Yes* under *Limit per user article*. This way you as admin must approve replies before they are visible. If there are many replies added you can just ignore them or delete them.
4. Configure other parameters to suit your needs.

To be able to add replies under list view in record you can insert this code (we will show reply button to main record author from specific group:
```
<?php
//start - show replies
$db = JFactory::getDbo();
$query = $db->getQuery(true);
$query = 'SELECT * FROM #__js_res_record WHERE published = 1 AND parent_id = '.$item->id;
$db->setQuery($query);
$review_reply = $db->loadObject();

if(isset($review_reply)) {
	echo '<div class="row-fluid review-reply pull-left well well-small">';
	echo '<h4 class="pull-left">' . "Owner's reply</h4><span>" . JText::sprintf('CONDATE', JHtml::_('date', $review_reply->ctime, $params->get('tmpl_core.item_time_format'))) . "</span>";
	echo '<p>' . @CobaltApi::renderField($review_reply, 255, 'list') . '</p>'; //255 is field ID
	echo '</div>';
}
//end - show replies

//start - show reply button
$parent = ItemsStore::getRecord($item->parent_id);
$user = JFactory::getUser();
$url = 'index.php?option=com_cobalt&view=form&field_id=255&type_id=3&section_id=5&fand='.$item->id.'&parent_id='.$item->id.'&return='.Url::back(); //255 is field ID, 3 is type ID, 5 is section ID, you can insert category id with &cat_id=43 where category ID is 43

if(in_array(10, $user->getAuthorisedGroups()) && $parent->user_id && $user->get('id') == $parent->user_id) { //10 is the group of user
	if(@$review_reply->published = 1) {
	echo '<a class="btn btn-primary btn-mini pull-right reply-link" rel="nofollow" href="' . JRoute::_($url) . '">' . JText::_('Reply') . '</a>';
	}
}
//end - show reply button
?>
```

Upper code is where all mojo for replies happens. If you want to play a little bit you can also try include this code:

```
<?php 
echo CommentHelper::listComments($this->submission_types[$item->type_id], $item); 
echo JHtml::_('content.prepare', $comment);
?>
```

#### Replies with Cobalt - Built in comments

You can also add replies with built in comments. This is something that might work (sorry, I didn't try it).

```
<?php //show comment as reply
$db = JFactory::getDbo();
$query = $db->getQuery(true);
$query->select('comment');
$query->from($db->quoteName('#__js_res_comments'));
$query->where('record_id = '.$item->id); 
$db->setQuery($query);
$review_reply = $db->loadObjectList();
foreach($review_reply as $reply) { 
	echo $reply->reply;
};
?>
```

## Cobalt - Built in comments
Work in progress...
