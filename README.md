@@ -0,0 +1,49 @@
 +#!/bin/bash 
+# حقوق الطبع والنشر لعام 2020 مؤلفو ChromiumOS 
+# يخضع استخدام كود المصدر هذا لترخيص نمط BSD الذي يمكن 
العثور عليه +# في الترخيص ملف. 
+ 
+set -e 
+ 
+if [[ "${UID:-$(id -u)}" != 0 ]]; ثم 
+ # لاحظ أنه نظرًا لأننا نتعامل مع متغيرات Sudo، فإن هذا البرنامج النصي 
+ # يرتد بشكل صريح إلى الجذر لكل ما يفعله - بهذه الطريقة 
+ # إذا قدم أي شخص حرمانًا مؤقتًا في إعداد Sudo، فلن يتمكن من كسر 
+ # منتصف الترقية. 
+ 
+ # مصدر فحص الصدفة=../common.sh 
+ . "$(dirname "$(dirname "$0")")/common.sh" || خروج 1 
+ 
+ Load_environment_whitelist 
+ echo "إعادة الكتابة باستخدام قائمة env ${ENVIRONMENT_WHITELIST[*]}" 
+ exec Sudo bash "$0" / "${USER}" "${ENVIRONMENT_WHITELIST[@]}" 
+ خروج 1 
+fi 
+ 
+ # الوصول إلى هنا يعني أننا جذرنا. 
+ 
+if [[ $# -lt 2 ]]; ثم 
+ echo "تم الاستدعاء بعدد خاطئ من الوسائط؛ الجذر المتوقع USER [المتغيرات]*" >&2 
+ خروج 1 
+fi 
+ 
+root=$1 
+username=$2 
+shift 2 
+set -- "$@" CROS_WORKON_SRCROOT PORTAGE_USERNAME 
+ 
+ cat > "${root}/etc/sudoers.d/90_cros" <<EOF 
+Defaults env_keep += "$*" 
+ 
+# adm يتيح للمستخدمين والبنيات الإلكترونية تشغيل sudo (على سبيل المثال، عداءو اختبار sysroot في النظام الأساسي 2). 
+%adm ALL=(ALL) NOPASSWD: ALL 
+${username} ALL=(ALL) NOPASSWD: ALL 
+ +# تبسيط عمليات التحقق من الخيار -v بسبب تداخل مجموعة الإعلان 
ومجموعات +# التكميلية الخاصة بالمستخدم . 
نحن لا نقوم بتعيين أي كلمات مرور، لذا قم بتعطيل السؤال. 
+# https://crbug.com/762445 
+Defaults Verifypw = Any 
+EOF 
+ 
+chmod 0444 "${root}/etc/sudoers.d/90_cros" 
+chown root:root "${root}/etc/sudoers .د/90_كروس"
