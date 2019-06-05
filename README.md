# Get Feed Without API
Get Feed / Twitter / Youtube / Instagram Without API

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

## Obtener Tweets de un Usuario
```php
function get_tweets_for($username, $get_dates=false){
	$page = file_get_contents("https://mobile.twitter.com/$username");
	preg_match_all(
		'{<div class="tweet-text" data-id="(\d+)">\s*<div class="dir-ltr" dir="ltr">\s*(.+?)\s*</div>\s*</div>}',
		$page,
		$matches
	);
	if (empty($matches[1])) return [];
	$ids   = array_values($matches[1]);
	$texts = array_values($matches[2]);
	unset($matches);
	$results = [];
	for ($i=1,$c=count($ids) ; $i<$c ; $i++ ){
		$results[] = [
			'id' 	=> $ids[$i],
			'text'  => strip_tags($texts[$i]),
		];
	}
	
	if ($get_dates) {
		$dates = [];
		preg_match_all(
			'{<a name="tweet_(\d+)"[^>]+>([^>]+)</a>}',
			$page,
			$dates
		);
		$dds = [];
		if (!empty($dates[1])) for ($i=0,$c=count($dates[1]) ; $i<$c ; $i++ ) $dds[$dates[1][$i]] = $dates[2][$i];
		if ($dds) foreach($results as &$tweet){
			if (isset($dds[$tweet['id']])){
				$tweet['date'] = $dds[$tweet['id']];
			}
		}
	}
	
	return $results;
}
```

## Obtener Post en Instagram de un Usuario
```php
function load_post_instagram(){
	$username='ESCRIBA SU USERNAME';
	$url     = sprintf("https://www.instagram.com/$username");
    $content = file_get_contents($url);
    $content = explode("window._sharedData = ", $content)[1];
    $content = explode(";</script>", $content)[0];
    $data    = json_decode($content, true);
    $edges = $data['entry_data']['ProfilePage'][0]['graphql']['user']['edge_owner_to_timeline_media']['edges'];
	$posts = array();
	foreach($edges as $k => $v){
		$posts[$k]['caption'] = $v['node']['edge_media_to_caption']['edges'][0]['node']['text'];
		$posts[$k]['img'] = $v['node']['display_url'];
	}
	echo json_encode($posts);
	die();
}
```
