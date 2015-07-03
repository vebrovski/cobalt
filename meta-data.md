# Generate meta data from Cobalt data

This is an easy way how to generate meta data from Cobalt categories, fields etc. You can find more about Joomla setMetaData method [here](https://docs.joomla.org/JDocument/setMetaData).

## Record list template

Let's look at adding meta data for records list first (this is useful only if you create custom record list templates). 

Cobalt structure is made of section and it's categories. We will try to generate meta data only for categories and in section you can add it manually in administration.

You could also create custom template parameter to turn generating meta data on or off. You can do that by opening .xml file of your template and add something like this:

```
<fieldset name="seo" label="SEO">
		<field name="metadata_auto" type="radio" class="btn-group" default="0" label="Auto generate meta data.">
			<option value="0">CNO</option>
			<option value="1">CYES</option>
		</field>
</fieldset>
```

You can now set meta with this methods:
- `setTitle();`
- `setMetadata('description', string $content);`
- `setMetaData('keywords', string $content);`

For populating `string $content` we can use different options:
- `$this->section->name`
- `$this->category->title`
- `$this->category->decsription`

Let's look an example:

```php
if($params->get('tmpl_params.metadata_auto') == 1) { // checks if template parameter is set to yes for creating meta data or not
	if(JFactory::getApplication()->input->getInt('cat_id') > 0) { // checks if it is category and not section
		$this->document->setTitle($this->category->title); // sets category title as meta title 
		$this->document->setMetaData( 'description', '$this->category->description'); // generate meta description from category description. Instead of $this->category->description you could use something like `substr($this->category->description,0,200) . '...'` to limit meta description to 200 characters.
		$this->document->setMetaData( 'keywords', $this->category->title . ', ' . $this->section->name . ', ' . ' some other custom words');
}
```

ou could also 