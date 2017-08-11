<?php
if (!$modx->hasPermission('ems_user_send')){
  return $modx->error->failure($modx->lexicon('access_denied'));
}

$userId = (int) $_REQUEST['userId'];
$users = $modx->getObject('dnUsers', $userId);

$message = $this->modx->getChunk('subject_approval', array('surname' => $users->get('surname'), 'name' => $users->get('name'), 'middlename' => $users->get('middlename'), 'number' => $users->get('barcode') ));

$Setting = $modx->getObject('modSystemSetting', 'registration_mail.fromname');
$fromname = $Setting->get('value'); 
        
$Setting = $modx->getObject('modSystemSetting', 'registration_mail.subject_approval');
$subjest = $Setting->get('value'); 
$subjest = str_replace("[[+name]]", $users->get('name'), $subjest);


	$message = $message;
    $email = $users->get('email');
    $fullname = $users->get('email');
	$subject = $users->get('surname').' '.$users->get('name');
	$from = 'aprokopova@ciseventsgroup.com';
	$fromName = $fromname;
	$sender = 'aprokopova@ciseventsgroup.com';
    $replyto = '';


    if ($replyto == '') $replyto = $sender;
    
    $modx->getService('mail', 'mail.modPHPMailer');
    if (!$this->modx->mail) return false;

    $this->modx->mail->set(modMail::MAIL_BODY, $message);
    $this->modx->mail->set(modMail::MAIL_FROM, $from);
    $this->modx->mail->set(modMail::MAIL_FROM_NAME, $fromName);
    $this->modx->mail->set(modMail::MAIL_SENDER, $sender);
    $this->modx->mail->set(modMail::MAIL_SUBJECT, $subject);
    $this->modx->mail->address('to', $email, $fullname);
    $this->modx->mail->address('reply-to', $replyto);
    $this->modx->mail->setHTML(true);
    $sent = $this->modx->mail->send();
    $this->modx->mail->reset();

    $this->modx->mail->mailer->SMTPDebug = 4;
    if (!$sent = $this->modx->mail->send()) {
        //$this->modx->log(modX::LOG_LEVEL_ERROR, 'err: ' . $this->modx->mail->mailer->ErrorInfo);//$mail->ErrorInfo
        die( $this->modx->mail->mailer->ErrorInfo);
    }
    $this->modx->mail->reset();
return '123';
    return $sent;

/*
$lib_path = MODX_BASE_PATH.'assets/libraries/';
set_include_path($lib_path);
require_once 'Zend/Mail.php';
require_once 'Zend/Mail/Transport/Smtp.php';

$config = array('port' => 26);//25
$transport = new Zend_Mail_Transport_Smtp('relay.emag.ru', $config);

$mail = new Zend_Mail('utf-8');
$mail->setHeaderEncoding(Zend_Mime::ENCODING_BASE64);
$mail->addHeader('MIME-Version', '1.0');
$mail->addHeader('Content-type', 'text/html; charset=utf-8');
$mail->addHeader ('X-Mailer', 'Mailjet-for-Magento/1.0', TRUE);
$mail->setFrom('aprokopova@ciseventsgroup.com', $fromname);
$mail->addTo($users->get('email'), $users->get('surname').' '.$users->get('name'));
$mail->setSubject('=?utf-8?B?'.base64_encode($subjest).'?=');
$mail->setBodyHTML($message);
 
try {
    $mail->send($transport);
    
    $users->set('status',1);
    $users->save();
    
    $details = array(
        'date'     => time(),
        'username' => $modx->user->get('username'),
        'fromname' => $fromname,
        'frommail' => 'aprokopova@ciseventsgroup.com',
        'toemail'  => $users->get('email'),	
        'toname'   => $users->get('surname').' '.$users->get('name'),
        'subject'  => $subjest,
        'body'     => $message
    );    
    $log = $modx->newObject('RegistrationLogMail', $details);
    
    if (!$log->save()) return $modx->error->failure($modx->lexicon('registration.log.save_error'));     
    
} catch (Zend_Mail_Transport_Exception $ex) {
    $this->xpdo->log(xPDO::LOG_LEVEL_ERROR, 'Zend_Mail_Transport_Exception for User('.$users->get('email').') - Mail was not sent: '.$ex->getMessage());
    $sent = false;
} catch (Exception $ex) {
    $this->xpdo->log(xPDO::LOG_LEVEL_ERROR, 'Mail for User('.$users->get('email').') - Mail was not sent: '.$ex->getMessage());
    $sent = false;
}
*/
return $modx->error->success('');



--------------------------------------------------------------------------------------------------------



<?php
if (!$modx->hasPermission('ems_user_send')){
  return $modx->error->failure($modx->lexicon('access_denied'));
}

$userId = (int) $_REQUEST['userId'];
$users = $modx->getObject('dnUsers', $userId);

$message = $this->modx->getChunk('subject_approval', array('surname' => $users->get('surname'), 'name' => $users->get('name'), 'middlename' => $users->get('middlename'), 'number' => $users->get('barcode') ));

$Setting = $modx->getObject('modSystemSetting', 'registration_mail.fromname');
$fromname = $Setting->get('value'); 
        
$Setting = $modx->getObject('modSystemSetting', 'registration_mail.subject_approval');
$subjest = $Setting->get('value'); 
$subjest = str_replace("[[+name]]", $users->get('name'), $subjest);

$lib_path = MODX_BASE_PATH.'assets/libraries/';
set_include_path($lib_path);
require_once 'Zend/Mail.php';
require_once 'Zend/Mail/Transport/Smtp.php';

$config = array('port' => 25);
$transport = new Zend_Mail_Transport_Smtp('relay.emag.ru', $config);

$mail = new Zend_Mail('utf-8');
$mail->setHeaderEncoding(Zend_Mime::ENCODING_BASE64);
$mail->addHeader('MIME-Version', '1.0');
$mail->addHeader('Content-type', 'text/html; charset=utf-8');
$mail->addHeader ('X-Mailer', 'Mailjet-for-Magento/1.0', TRUE);
$mail->setFrom('aprokopova@ciseventsgroup.com', $fromname);
$mail->addTo($users->get('email'), $users->get('surname').' '.$users->get('name'));
$mail->setSubject('=?utf-8?B?'.base64_encode($subjest).'?=');
$mail->setBodyHTML($message);
 
try {
    $mail->send($transport);
    
    $users->set('status',1);
    $users->save();
    
    $details = array(
        'date'     => time(),
        'username' => $modx->user->get('username'),
        'fromname' => $fromname,
        'frommail' => 'aprokopova@ciseventsgroup.com',
        'toemail'  => $users->get('email'),	
        'toname'   => $users->get('surname').' '.$users->get('name'),
        'subject'  => $subjest,
        'body'     => $message
    );    
    $log = $modx->newObject('RegistrationLogMail', $details);
    
    if (!$log->save()) return $modx->error->failure($modx->lexicon('registration.log.save_error'));     
} catch (Zend_Mail_Transport_Exception $e) {
    $this->modx->log(modX::LOG_LEVEL_ERROR,'Zend_Mail_Transport_Exception for User('.$users->get('email').') - Mails were not accepted for sending: '.$e->getMessage());
}

return $modx->error->success('');

