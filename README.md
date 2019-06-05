# function_feed_twitter_youtube_instagram
Get Feed / Twitter / Youtube / Instagram

## Obtener Videos de Un Canal de Youtube
```php
function GetVideosYoutubeChannel($url_channel){
	function json_prepare_xml($domNode) {
	  foreach($domNode->childNodes as $node) {
		if($node->hasChildNodes()) {
		  json_prepare_xml($node);
		} else {
		  if($domNode->hasAttributes() && strlen($domNode->nodeValue)){
			 $domNode->setAttribute("nodeValue", $node->textContent);
			 $node->nodeValue = "";
		  }
		}
	  }  
	}
	$xmlfile = $url_channel;
	$dom = new DOMDocument();
	$dom->loadXML( file_get_contents($xmlfile) );
	json_prepare_xml($dom);
	$sxml = simplexml_load_string( $dom->saveXML() );
	$json = json_decode( json_encode( $sxml ) );
	$json_entry = $json->entry;
	if(is_array($json_entry)):
	$display_array = $json_entry;
	else:
	$display_array = '';
	endif;
	return $display_array;
}
```
