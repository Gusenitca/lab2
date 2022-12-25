# lab2

Задание

![изображение](https://user-images.githubusercontent.com/90793439/205446032-19686273-b527-4051-9345-27d7a54485ed.png)

Ход работы

![изображение](https://user-images.githubusercontent.com/90793439/205446385-2727c064-e3bb-43de-b820-0996cef2fe04.png)

1. Пользовательский интерфейс
![изображение](https://user-images.githubusercontent.com/90793439/209466673-43805705-d6ad-4ebe-b2ab-62637efbc1cf.png)
Форма отправки сообщений
![изображение](https://user-images.githubusercontent.com/90793439/209467688-caf85cb0-7b29-497a-a1f7-efbc91e54e80.png)

Интерфейс
2. Пользовательский сценарий работы

Первоначально пользователь попадает на форму входа index.php.
Если ранее была произведена регистрация, то он вводит логин, пароль, нажимает на кнопку "Log in" и входит в свой профиль. В случае корректного ввода пользователь перенаправляется на главную страницу profile.php.
Если же введены какие-то данные неверно, то происходит возврат на страницу входа, вверху формы будет написано сообщение об ошибке.
Если же изначально пользователь не имеет профиля, то он может перейти по ссылке “Sign up” и зарегистрироваться. В данном случае пользователь перенаправляется на страницу регистрации signup.php, где необходимо ввести логин, почту, пароль и подтверждение пароля и нажать на кнопку “Sign up”. В случае корректного ввода он перенаправляется на страницу index.php, где нужно совершить вход в свой профиль. Если же пользователь ввел какие-то данные неверно, то происходит возврат на страницу index.php, вверху будет написано сообщение об ошибке.
Если пользователь случайно перешел по ссылке регистрации, то он может нажать на ссылку “Have account - log in”, которая вернет его обратно на форму входа.
Если пользователь забыл пароль от существующего профиля и не может войти, то он может перейти по ссылке “Forgot your password? Recover it!” на странице входа, тем самым создать новый пароль. В данном случае пользователь перенаправляется на страницу изменения пароля recovery.php, где необходимо ввести логин, пароль и подтверждение пароля и нажать на кнопку “Recovery”. В случае корректного ввода пользователь перенаправляется на страницу index.php. Если же он ввел что-то неправильно, то происходит возврат на страницу восстановления пароля, вверху будет написано сообщение об ошибке.
Когда пользователь попадает на главную страницу, то у него есть две ссылки: “Log out” и “Update password”. Первая ссылка перенаправляет пользователя на форму входа, при этом он выходит из аккаунта.
Вторая ссылка перенаправляет на страницу смены пароля update.php. На этой странице нужно ввести старый, новый пароль и подтверждение нового пароля и нажать на кнопку “Update”. В случае корректного ввода пользователь перенаправляется на главную страницу profile.php и на ней вверху отображается сообщение об успешной смене пароля. Если же он ввел какие-то данные неверно, то происходит возврат на страницу обновления пароля, вверху будет написано сообщение об ошибке.
3. API сервера и хореография

Хореография
4. Структура базы данных
Название 	Тип 	Длина 	NULL 	Описание
id 	INT 		NO 	Автоматический идентификатор пользователя
login 	VARCHAR 	100 	NO 	Логин пользователя
email 	VARCHAR 	255 	NO 	Почта пользователя
password 	VARCHAR 	60 	NO 	Хешированный пароль
5. Алгоритм

Вход
Вход

Регистрация
Регистрация

Восстановление пароля
Восстановление пароля
6. HTTP запрос/ответ

Запрос
![изображение](https://user-images.githubusercontent.com/90793439/209467962-c9b975b7-8655-4d7e-bb55-04e17cc7af73.png)

7. Значимые фрагменты кода


Отправка сообщения

function setComments($conn) {
    if (isset($_POST['commentSubmit'])) {
        $uid = $_POST['uid'];
        $date = $_POST['date'];
        $message = $_POST['message'];
        $likes = $_POST['likes'];
        $dislikes = $_POST['dislikes'];
        $file = $_FILES['img'];
        $name = gen_str(5).".png";
        (@copy($file["tmp_name"], __DIR__.'/img/'.$name));
        $sql = "INSERT INTO comments (uid, date, message, likes, dislikes, image_id) VALUES ('$uid', '$date', '$message', '$likes', '$dislikes', '$name')";
        $result = $conn->query($sql);
        header('Location: index.php' );
    }
}

Добавление лайка

function likeSubmit($conn,$row) {
    if(isset($_POST[$row['cid']])) {
        $cid = $row['cid'];
        $likes = $row['likes']+1;
        $query = "UPDATE comments SET likes = '$likes' WHERE cid = '$cid'";
        $result = mysqli_query($conn, $query);
    }
}

Вывод сообщений

function getComments($conn){
    $sql = "SELECT * FROM comments";
    $result = $conn->query($sql);
    $max_page_posts = 100;
    $last_post = mysqli_num_rows($result);
    $i = 0;
    while(($row = $result->fetch_assoc())){
        if (($last_post - $i) > $max_page_posts ){
        }
        else{
            echo "<div class='comment-box'><p>";
            echo $row['uid']."<br>";
            echo $row['date']."<br>";
            echo $row['message']."<br>";
            $img_id = $row['image_id'];
            echo "<br>";
            if ($img_id) {
                echo "<td><img style = 'width:390px;' src = 'img/".$img_id."' alt = 'Тут могло быть изображение'/> </td>";
                }
            echo "<div>  <form method='POST' action='".likeSubmit($conn,$row)."'>  <button type='submit' name='".$row['cid']."' class='likeSubmit'>Like</button>  Likes: ".$row["likes"]."  </form></div>";
            echo "<br>";
            echo "<div>  <form method='POST' action='".dislikeSubmit($conn,$row)."'>  <button type='submit' name='".$row['cid']."' class='dislikeSubmit' style='  background-color: #ff0000; color: white; border: none; cursor: pointer; opacity: 0.9;'>Dislike</button>  Dislikes: ".$row["dislikes"]."  </form></div>";
            echo "<hr>";
            echo "<p></div>";
        }
        $i++;
    }
}

Вывод

В ходе выполнения лабораторной работы спроектировали и разработали систему сайта на котором ты можешь переписываться сам с собой. отправлять картинки и поднимать себе настроение
