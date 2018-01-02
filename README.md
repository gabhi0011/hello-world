# hello-world

<?php
$ROOT_URL = 'http://services.uplynk.com';
$OWNER = 'c56ea4014685bc74c0a375236cc5a735';
$SECRET = 'GESKwbpWxQ/QhHFmhTZLLu3rYeNuK4gYrWwlCLnT';

function encode_array($args)
{
  if(!is_array($args)) return false;
  $c = 0;
  $out = '';
  foreach($args as $name => $value)
  {
    if($c++ != 0) $out .= '&';
    $out .= urlencode("$name").'=';
    if(is_array($value))
    {
      $out .= urlencode(serialize($value));
    }else{
      $out .= urlencode("$value");
    }
  }
  return $out . "\n";
}

function Call($uri, $msg=array())
{
    global $ROOT_URL, $OWNER, $SECRET;

    $msg['_owner'] = $OWNER;
    $msg['_timestamp'] = time();
    $msg = json_encode($msg);
    $msg = base64_encode(gzcompress($msg,9));
    $sig = hash_hmac('sha256', $msg, $SECRET);
    $sig = trim($sig);
    $body = encode_array(array('msg'=>$msg, 'sig'=>$sig));
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $ROOT_URL . $uri);
    curl_setopt($ch, CURLOPT_POST,1);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $body);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER,1);
    $result = curl_exec($ch);
    curl_close($ch);
    return $result;
}
