---
title: สิ่งเทพ ๆ เรียก Collection ใน Laravel ที่หลายคนไม่ค่อยรู้
image: "./laravel-collection-laravel-collection-signed.png"
category: Laravel
excerpt: "ไม่ได้เขียนเกี่ยวกับ Laravel นาน พอดีนึกได้ว่ามีของอย่างนึงใน Laravel ที่ผมไปเปิดคอร์สหลาย ๆ อันมา แม้กระทั่งผมที่สอนเองก็ไม่ได้หยิบเรื่องนี้มาสอนเท่าไหร่คือเรื่องของ Collection"
date: "2017-11-19T22:12:03.284Z"
author: arnondora
template: normal
type: post
isFeatured: false
status: published
---

ไม่ได้เขียนเกี่ยวกับ Laravel นาน พอดีนึกได้ว่ามีของอย่างนึงใน Laravel ที่ผมไปเปิดคอร์สหลาย ๆ อันมา แม้กระทั่งผมที่สอนเองก็ไม่ได้หยิบเรื่องนี้มาสอนเท่าไหร่คือเรื่องของ Collection ใน Laravel มันเป็นอะไรที่ถ้าเราใช้เป็น มันจะทำให้ Code เราดูสะอาดตาเอามาก ๆ

## เราจะสร้าง Collection ใน Laravel ได้ยังไง ?

ต้องบอกก่อนว่า Collection เป็น Class นึงที่อยู่ใน **Illuminate\Support\Collection** มันจะเข้ามาช่วยให้เราสามารถจัดการข้อมูลจำพวก Array ได้อย่างง่ายดายมาก แต่ก่อนจะไปใช้กัน เราต้องมาดูกันก่อนว่า เราจะได้ Collection มาได้ยังไง

    $myCollection = collect(['Hello', 'Me', 'Name"]);

คำสั่งด้านบนเป็นวิธีหนึ่งที่เราจะสามารถแปลง Array ให้อยู่ในรูปของ Collection ได้ หรืออีกวิธีนึงที่เราได้ Collection มาแบบไม่รู้ตัวคือ

    $myCollection = User::where('BirthYear', '>', '1990');

การที่เราเรียกพวก Query Builder หรือ Eloquent สิ่งที่มันคืนค่ากลับมาให้เราก็คือ Collection (ถ้าสงสัยก็ลองรันใน tinker ดูแล้วจะเห็นเลยว่ามันคืออะไร) ทีนี้ถามว่า ถ้าเราต้องการให้มันกลับมาเป็น Array เหมือนเดิมละ เราจะทำยังไง ?

    $collectArray = $myCollection->all();

จากคำสั่งด้านบนก็จะทำให้เราได้ Array กลับออกมาแล้ว นอกจากนั้นเรายังใช้ get() ในการดึงข้อมูลใน Field ที่เราต้องการออกมาได้ แต่นอกจากนั้นมันยังมีคำสั่งให้เราสามารถใช้ได้แบบเท่ ๆ อีกด้วย วันนี้เลยจะหยิบอันที่เราน่าจะใช้บ่อย ๆ มาให้อ่านกัน

## avg()

อันนี้อ่านจะชื่อแล้วน่าจะเข้าใจว่ามันทำอะไรได้ มันคือการหาค่าเฉลี่ยนี่เอง โดยเราสามารถให้มันหาได้โดยใช้คำสั่งแบบนี้

    $myCollection->avg('age');

ง่าย ๆ คือให้เราเรียก avg() ออกมา ข้างในเราก็ใส่เป็น Field ที่เราต้องการจะหาค่าเฉลี่ยลงไป นอกจากนั้น เรายังสามารถหา max,min,median หรือแม้กระทั่งทำ Set Operation อย่าง Union และ Intersect ก็ยังได้

## where()

เป็นคำสั่งที่ทำให้เราสามารถเลือกข้อมูลออกมาตามที่เราต้องการได้ เหมือนกับที่เราใช้กับ SQL เช่น

    $myCollection->where('age',20);

คำสั่งด้านบนคือการที่เราขอหยิบ Member ที่อายุ**เท่ากับ** 20 ขึ้นมา แต่ถ้าเราต้องการหลาย ๆ อายุก็ทำได้เหมือนกันโดยใช้ whereIn() เช่น

    $myCollection->whereIn('age',[15,20]);

เราจะเห็นว่าจากตอนแรกเราก็ใส่ค่าเข้าไปเป็น Integer ปกติแต่ใน whereIn เราจะเติมลงไปเป็น Array ของ Value ทีี่เราต้องการหาออกมาได้เลย แต่ถ้าเราต้องการหลาย ๆ เงื่อนไข การใช้ where() ต่อ ๆ กันไปเรื่อย ๆ ก็ยังสามารถทำได้เลย

## filter()

ถ้าคิดว่าการใช้ where() ยังเด็ดไม่พอ เราขอนำเสนอ filter() ที่จะช่วยให้เราดูดข้อมูลได้ดั่งใจมากขึ้น

    $myCollection->filter( function ($value, $key) {
        return $value['age'] > 2;
    });

จากด้านบนเป็นการที่เราดูด Record ที่อายุน้อยกว่า 2 ออกไป เราจะเห็นว่าการใช้ Filter เราจะควบคุมได้มากกว่า where เพราะเราสามารถจัดการทั้งหมดได้ด้วย Callback ข้างล่าง แต่ข้อเสียคือมันทำให้ Code ดูไม่ค่อยงามเท่าไหร่ แนะนำว่าถ้าอะไรมันสามารถใช้ where หรืออื่น ๆ ได้ก็ใช้เถอะครับ จะได้ไม่ต้องมีอะไรงอกออกมาเป็นอีกบรรทัดแบบนี้ มันทำให้ Code เราสะอาดกว่าเยอะเลย

## sort()

การเรียงข้อมูลอาจจะไม่่ใช่เรื่องสนุกสำหรับใครบางคนก็ได้ Laravel เลยเตรียมคำสั่งสำหรับการเรียงข้อมูลมาให้เราเรียบร้อยแล้ว ง่าย ๆ คือเราสามารถเรียกแล้วบอกมันว่าจะให้เรียงผ่าน Field ไหน แล้วมันก็สามารถเรียงให้เราได้เลย เช่นด้านล่างนี้

    $myCollection->sortBy('age')

หรือถ้าเราต้องการให้มันเรียงกลับด้านกัน sortByDesc() ก็ใช้ได้เช่นกัน หรืออยากได้ท่ายากกว่านั้น เรายังสามารถใส่ Callback ลงไปใน sort ได้ด้วยเช่นกัน

    $mycollection->sortBy(function ($value, $key){
        return count($value['friends']);
    });

จากด้านบนคือการที่เราจะเรียงตามจำนวนของเพื่อนโดยที่เราไม่ได้เก็บจำนวนเพื่อน แต่เราเก็บชื่อของเพื่อน เราก็แค่นับเพื่อนในแต่ละ Member แล้วเอามา sort ก็ได้เช่นกัน

## groupBy()

ถ้าเราต้องการจัดกลุ่มข้อมูลเราก็สามารถทำได้ผ่านคำสั่งที่ชื่อว่า groupBy() เช่น

    $myCollection->sortBy('surname');

จากตรงนี้เราก็จะได้คนที่มีนามสกุลเหมือนกันออกมาโดยแยกออกมาเป็นแต่ละนามสกุล แล้วข้างในแต่ละนามสกุลก็จะเป็น Record ของคนที่นามสกุลนั้น ๆ

## chunk()

อีกคำสั่งที่น่าจะได้ใช้กันคือ chunk() ที่จะช่วยให้เราสามารถแบ่งข้อมูลเราออกเป็นส่วน ๆ ตามจำนวนที่้เราต้องการได้ เช่นเราบอกว่า เราอยากได้สัก 10 record จาก 100 เราก็สามารถเรียก chunk() แล้วมันก็จะแบ่งออกมาเป็นทีละ 10 อันได้โดยง่ายมาก ๆ ลองมาดูตัวอย่างกัน

    $myCollection->chunk(10)

เท่านี้เราก็จะได้ก้อนละ 10 records จากทั้งหมดแล้ว หรือถ้าเราต้องการให้มันกลับไปเป็นเหมือนเดิม เราก็สามารถใช้ collapse() ให้การทำให้กลายเป็นแบบที่เรายังไม่แบ่งได้

## map(), reduce(), transform()

หลาย ๆ คนน่าจะคุ้นเคยกับ 3 คำสั่งนี้ดี อย่างผมที่ไม่ค่อยคุ้นเคยกับ Functional Programming สักเท่าไหร่ ตอนแรกก็จะมึน ๆ เล็กน้อยเวลาเอาไปใช้ เริ่มที่อันแรกคือ map() คือการที่เราให้มันวิ่งไปในทุก ๆ record และให้มันทำอะไรบางอย่างเช่นนับบจำนวนเพื่อนของแต่ละ User ดังนี้

    $numOfFriends = $myCollection->map( function($value,key) {
        return count($value['friends']);
    });

สิ่งที่คล้าย ๆ กันกับ map() คือ transform() ถ้าดูเผิน ๆ เราจะเห็นว่ามันทำสิ่งเดียวกันคือการที่เราวิ่งไปทุก ๆ record และได้ค่าใหม่อะไรบางอย่างลงมา แต่สิ่งที่ต่างระหว่าง map() และ transform() คือ map() จะสร้าง collection ใหม่ออกมา (ถ้าเราเห็นใน Code ด้านบนคือ ผมเอาตัวแปรมารับ Collection ใหม่) แต่ในขณะที่ transform() ชื่อมันก็บอกอยู่คือ มันจะเข้าไปแก้ใน Collection นั้น ๆ เลย เราไม่ต้องเอาตัวแปรมารับ สุดท้ายคือ reduce() ที่จะต่างกับ map() และ transform() คือมันจะส่งค่ากลับเพียงค่าเดียว เช่นเราบอกว่าเราอยากได้ผลรวมของจำนวนเพื่อนทั้งหมด ของทุก User เราก็สามารถเขียนได้ว่า

    $numAllFriends = $myCollection->reduce( function($sum, $value) {
        return $sum   count($value['friends']);
    });

จะเห็นว่าจาก Code ด้านบนมันจะวิ่งเข้าไปในทุก ๆ Record และบวกค่าขึ้นไปเรื่อย ๆ เราก็สามารถทำคำสั่งเหล่านี้ไปใช้ทำอะไรได้เยอะมาก ๆ เหมือนกับท่ามารเลยละ

## each()

จาก 3 คำสั่งเมื่อกี้มันเป็นการวิ่งแล้วได้ค่าอะไรสักอย่างแล้วมีผลต่อ Iteration ต่อไปแต่ each จะเทียบเท่ากับที่เราใช้ foreach เลย ตัวอย่างเช่น

    $friendsNum = 0;

    $myCollection->each( function ($value, $key) {
        $friendsNum  = count($value['friends]);
    });

จากตัวอย่างด้านบนเป็นการหาผลรวมของจำนวนเพื่อนเหมือนกับตัวอย่างก่อนหน้านี้ เราจะเห็นว่า มันสามารถทำได้เหมือนกัน แต่จะเห็นว่าเราต้องกำหนดตัวแปรมารับอีก ซึ่งมันเปลืองไปตั้งบรรทัดนึง และทำให้ Code ไม่ดูดีเท่ากับการใช้ reduce() เลย ถ้าพูดถึงเรื่อง Performance เทียบกับ each() และ foreach() ธรรมดาจะตอบว่า foreach ธรรมดาเร็วที่สุด แต่หน่วยที่เราเทียบกันอยู่ เราคุยกันในหลัก Microsecond ฉะนั้นก็ไม่ต้องกังวลเรื่องนี้เลย เว้นแต่เอาไปแข่งกันเรื่องความเร็วกันจริง ๆ แต่ข้อดีของ each() คือมันสามารถเอาไป stack กับคำสั่งอื่น ๆ ได้ทำให้อะไร ๆ มันง่ายขึ้นและจบในบรรทัดเดียวจริง ๆ

## สรุป

Collection เป็นอะไรที่ทำให้ชีวิตเราง่ายขึ้นมาก ๆ แต่หลาย ๆ คนมักจะมองข้ามมัน แล้วไปนั่ง Implement กันเอง (เรานี่แหละ) แต่พอได้มาอ่านจริง ๆ แล้วก็ เออหว่ะ แล้วตรูจะมานั่งเขียนเองเพื่อ ??? อีกอย่างคือมันสามารถเอาแต่ละคำสั่งมา Stack กันได้ด้วย ทำให้เราสามารถจัดการข้อมูลพวกนี้ได้ง่ายขึ้นอีก จริง ๆ แล้วมันยังมีอีกหลายคำสั่งให้เลือกสรรมาใช้กันนะ ก็ลองเข้าไปอ่านใน Document ได้ สำหรับวันนี้สวัสดี สวีดัส บรัย ~