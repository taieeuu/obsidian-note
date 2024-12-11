---
Date: 2024-02-07
tags:
  - 資安
---
## Protection

- 使用內置會話管理  
    現代框架實現了安全的、不可猜測的會話 ID。如果您使用的是最新版本的 Web 工具包，請檢查會話 ID 是如何生成的。下面的代碼示例演示了許多生成會話 ID 的好方法。
    
- 防篡改您的 Cookie  
    Rails 和Django等框架 允許您對 cookie 進行簽名。這意味著服務器將能夠判斷 cookie 自從它與 Set-Cookie標頭一起發送到瀏覽器後是否已被操縱。數據被篡改的任何跡像都將使會話無效。
## 代碼示例
### Python
```
 def get_random_string(length=12,
                allowed_chars='abcdefghijklmnopqrstuvwxyz'
                              'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'):
    """
    Returns a securely generated random string.
    The default length of 12 with the a-z, A-Z, 0-9 character set returns
    a 71-bit value. log_2((26+26+10)^12) =~ 71 bits
    """
    if not using_sysrandom:
        # This is ugly, and a hack, but it makes things better than
        # the alternative of predictability. This re-seeds the PRNG
        # using a value that is hard for an attacker to predict, every
        # time a random string is required. This may change the
        # properties of the chosen random sequence slightly, but this
        # is better than absolute predictability.
        random.seed(
            hashlib.sha256(
                ("%s%s%s" % (
                    random.getstate(),
                    time.time(),
                    settings.SECRET_KEY)).encode('utf-8')
            ).digest())
    return ''.join(random.choice(allowed_chars) for i in range(length))
    ```
```
### Ruby
def generate_sid  
ActiveSupport::SecureRandom.hex(16)  
end
### Java
**Tomcat**

```
 /*
  * Licensed to the Apache Software Foundation (ASF) under one or more
  * contributor license agreements.  See the NOTICE file distributed with
  * this work for additional information regarding copyright ownership.
  * The ASF licenses this file to You under the Apache License, Version 2.0
  * (the "License"); you may not use this file except in compliance with
  * the License.  You may obtain a copy of the License at
  *
  *      http://www.apache.org/licenses/LICENSE-2.0
  *
  * Unless required by applicable law or agreed to in writing, software
  * distributed under the License is distributed on an "AS IS" BASIS,
  * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  * See the License for the specific language governing permissions and
  * limitations under the License.
  */
 package org.apache.catalina.util;

 public class StandardSessionIdGenerator extends SessionIdGeneratorBase {

     @Override
     public String generateSessionId(String route) {

         byte random[] = new byte[16];
         int sessionIdLength = getSessionIdLength();

         // Render the result as a String of hexadecimal digits
         // Start with enough space for sessionIdLength and medium route size
         StringBuilder buffer = new StringBuilder(2 * sessionIdLength + 20);

         int resultLenBytes = 0;

         while (resultLenBytes < sessionIdLength) {
             getRandomBytes(random);
             for (int j = 0;
             j < random.length && resultLenBytes < sessionIdLength;
             j++) {
                 byte b1 = (byte) ((random[j] & 0xf0) >> 4);
                 byte b2 = (byte) (random[j] & 0x0f);
                 if (b1 < 10)
                     buffer.append((char) ('0' + b1));
                 else
                     buffer.append((char) ('A' + (b1 - 10)));
                 if (b2 < 10)
                     buffer.append((char) ('0' + b2));
                 else
                     buffer.append((char) ('A' + (b2 - 10)));
                 resultLenBytes++;
             }
         }

         if (route != null && route.length() > 0) {
             buffer.append('.').append(route);
         } else {
             String jvmRoute = getJvmRoute();
             if (jvmRoute != null && jvmRoute.length() > 0) {
                 buffer.append('.').append(jvmRoute);
             }
         }

         return buffer.toString();
     }

 }
```

**Jetty**

```
/* ------------------------------------------------------------ */
/**
 * Create a new session id if necessary.
 *
 * @see org.eclipse.jetty.server.SessionIdManager#newSessionId(javax.servlet.http.HttpServletRequest, long)
 */
@Override
public String newSessionId(HttpServletRequest request, long created)
{
    synchronized (this)
    {
        if (request==null)
            return newSessionId(created);

        // A requested session ID can only be used if it is in use already.
        String requested_id=request.getRequestedSessionId();
        if (requested_id!=null)
        {
            String cluster_id=getClusterId(requested_id);
            if (idInUse(cluster_id))
                return cluster_id;
        }

        // Else reuse any new session ID already defined for this request.
        String new_id=(String)request.getAttribute(__NEW_SESSION_ID);
        if (new_id!=null&&idInUse(new_id))
            return new_id;

        // pick a new unique ID!
        String id = newSessionId(request.hashCode());

        request.setAttribute(__NEW_SESSION_ID,id);
        return id;
    }
}

/* ------------------------------------------------------------ */
public String newSessionId(long seedTerm)
{
    // pick a new unique ID!
    String id=null;
    while (id==null||id.length()==0||idInUse(id))
    {
        long r0=_weakRandom
                ?(hashCode()^Runtime.getRuntime().freeMemory()^_random.nextInt()^((seedTerm)<<32))
                :_random.nextLong();
        if (r0<0)
            r0=-r0;

        // random chance to reseed
        if (_reseed>0 && (r0%_reseed)== 1L)
        {
            if (LOG.isDebugEnabled())
                LOG.debug("Reseeding {}",this);
            if (_random instanceof SecureRandom)
            {
                SecureRandom secure = (SecureRandom)_random;
                secure.setSeed(secure.generateSeed(8));
            }
            else
            {
                _random.setSeed(_random.nextLong()^System.currentTimeMillis()^seedTerm^Runtime.getRuntime().freeMemory());
            }
        }

        long r1=_weakRandom
            ?(hashCode()^Runtime.getRuntime().freeMemory()^_random.nextInt()^((seedTerm)<<32))
            :_random.nextLong();
        if (r1<0)
            r1=-r1;

        id=Long.toString(r0,36)+Long.toString(r1,36);

        //add in the id of the node to ensure unique id across cluster
        //NOTE this is different to the node suffix which denotes which node the request was received on
        if (_workerName!=null)
            id=_workerName + id;

    }
    return id;
}
```
### C#
[ASP.NET](http://asp.net/)  
根據 MSDN：

每個活動的 [ASP.NET](http://asp.net/) 會話都使用僅由 URL 允許的字符組成的 120 位字符串進行標識。會話 ID 是使用隨機數生成器 (RNG) 加密提供程序生成的。服務提供商返回一個由 15 個隨機生成的數字組成的序列（15 字節 x 8 位 = 120 位）。然後將隨機數數組映射到有效的 URL 字符並作為字符串返回。
### Node
Express.js

```
function generateSessionId(sess) {
  return uid(24);
}
```
### PHP
```
PHPAPI char *php_session_create_id(PS_CREATE_SID_ARGS) /* {{{ */
{
  PHP_MD5_CTX md5_context;
  PHP_SHA1_CTX sha1_context;
#if defined(HAVE_HASH_EXT) && !defined(COMPILE_DL_HASH)
  void *hash_context = NULL;
#endif
  unsigned char *digest;
  int digest_len;
  int j;
  char *buf, *outid;
  struct timeval tv;
  zval **array;
  zval **token;
  char *remote_addr = NULL;

  gettimeofday(&tv, NULL);

  if (zend_hash_find(&EG(symbol_table), "_SERVER", sizeof("_SERVER"), (void **) &array) == SUCCESS &&
    Z_TYPE_PP(array) == IS_ARRAY &&
    zend_hash_find(Z_ARRVAL_PP(array), "REMOTE_ADDR", sizeof("REMOTE_ADDR"), (void **) &token) == SUCCESS
  ) {
    remote_addr = Z_STRVAL_PP(token);
  }

  /* maximum 15+19+19+10 bytes */
  spprintf(&buf, 0, "%.15s%ld%ld%0.8F", remote_addr ? remote_addr : "", tv.tv_sec, (long int)tv.tv_usec, php_combined_lcg(TSRMLS_C) * 10);

  switch (PS(hash_func)) {
    case PS_HASH_FUNC_MD5:
      PHP_MD5Init(&md5_context);
      PHP_MD5Update(&md5_context, (unsigned char *) buf, strlen(buf));
      digest_len = 16;
      break;
    case PS_HASH_FUNC_SHA1:
      PHP_SHA1Init(&sha1_context);
      PHP_SHA1Update(&sha1_context, (unsigned char *) buf, strlen(buf));
      digest_len = 20;
      break;
#if defined(HAVE_HASH_EXT) && !defined(COMPILE_DL_HASH)
    case PS_HASH_FUNC_OTHER:
      if (!PS(hash_ops)) {
        php_error_docref(NULL TSRMLS_CC, E_ERROR, "Invalid session hash function");
        efree(buf);
        return NULL;
      }

      hash_context = emalloc(PS(hash_ops)->context_size);
      PS(hash_ops)->hash_init(hash_context);
      PS(hash_ops)->hash_update(hash_context, (unsigned char *) buf, strlen(buf));
      digest_len = PS(hash_ops)->digest_size;
      break;
#endif /* HAVE_HASH_EXT */
    default:
      php_error_docref(NULL TSRMLS_CC, E_ERROR, "Invalid session hash function");
      efree(buf);
      return NULL;
  }
  efree(buf);

  if (PS(entropy_length) > 0) {
#ifdef PHP_WIN32
    unsigned char rbuf[2048];
    size_t toread = PS(entropy_length);

    if (php_win32_get_random_bytes(rbuf, MIN(toread, sizeof(rbuf))) == SUCCESS){

      switch (PS(hash_func)) {
        case PS_HASH_FUNC_MD5:
          PHP_MD5Update(&md5_context, rbuf, toread);
          break;
        case PS_HASH_FUNC_SHA1:
          PHP_SHA1Update(&sha1_context, rbuf, toread);
          break;
# if defined(HAVE_HASH_EXT) && !defined(COMPILE_DL_HASH)
        case PS_HASH_FUNC_OTHER:
          PS(hash_ops)->hash_update(hash_context, rbuf, toread);
          break;
# endif /* HAVE_HASH_EXT */
      }
    }
#else
    int fd;

    fd = VCWD_OPEN(PS(entropy_file), O_RDONLY);
    if (fd >= 0) {
      unsigned char rbuf[2048];
      int n;
      int to_read = PS(entropy_length);

      while (to_read > 0) {
        n = read(fd, rbuf, MIN(to_read, sizeof(rbuf)));
        if (n <= 0) break;

        switch (PS(hash_func)) {
          case PS_HASH_FUNC_MD5:
            PHP_MD5Update(&md5_context, rbuf, n);
            break;
          case PS_HASH_FUNC_SHA1:
            PHP_SHA1Update(&sha1_context, rbuf, n);
            break;
#if defined(HAVE_HASH_EXT) && !defined(COMPILE_DL_HASH)
          case PS_HASH_FUNC_OTHER:
            PS(hash_ops)->hash_update(hash_context, rbuf, n);
            break;
#endif /* HAVE_HASH_EXT */
        }
        to_read -= n;
      }
      close(fd);
    }
#endif
  }

  digest = emalloc(digest_len + 1);
  switch (PS(hash_func)) {
    case PS_HASH_FUNC_MD5:
      PHP_MD5Final(digest, &md5_context);
      break;
    case PS_HASH_FUNC_SHA1:
      PHP_SHA1Final(digest, &sha1_context);
      break;
#if defined(HAVE_HASH_EXT) && !defined(COMPILE_DL_HASH)
    case PS_HASH_FUNC_OTHER:
      PS(hash_ops)->hash_final(digest, hash_context);
      efree(hash_context);
      break;
#endif /* HAVE_HASH_EXT */
  }

  if (PS(hash_bits_per_character) < 4
      || PS(hash_bits_per_character) > 6) {
    PS(hash_bits_per_character) = 4;

    php_error_docref(NULL TSRMLS_CC, E_WARNING, "The ini setting hash_bits_per_character is out of range (should be 4, 5, or 6) - using 4 for now");
  }

  outid = emalloc((size_t)((digest_len + 2) * ((8.0f / PS(hash_bits_per_character)) + 0.5)));
  j = (int) (bin_to_readable((char *)digest, digest_len, outid, (char)PS(hash_bits_per_character)) - outid);
  efree(digest);

  if (newlen) {
    *newlen = j;
  }

  return outid;
}
```
