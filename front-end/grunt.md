# [Grunt](http://gruntjs.com/)

## [grunt-spritesmith](https://github.com/Ensighten/grunt-spritesmith)
소위 [CSS Sprites](https://css-tricks.com/css-sprites/)라는 기법은 웹페이지의 리소스 로딩속도를
높히는 방법으로 작은 파일을 여러개 HTTP로 요청하는 것보다 큰 파일을 하나 요청하는 것이 빠르므로 작은
아이콘 파일등을 하나로 합쳐서 CSS에서 배경 이미지의 위치를 이동시켜서 표시하는 방법이다.

이를 위해 [Sprite Cow](http://www.spritecow.com/)라는 온라인 도구를 사용해 봤으나 위치를
정확히 잡는 것이 어려워서 포기하고 포토샵으로 작업했으나 포토샵은 배경이 없는 이미지는 크기를 정확히
잡아주지 않으므로 수동으로 배경레이어를 넣어서 크기를 정해주어야 했다. 작업시간도 작업시간이지만
새로운 아이콘이 생기거나 변경될 경우 계속 바꿔주면 크기나 위치도 다시 잡아야 해서 유지보수면에서
관리하기가 어려웠다.

그래서 [grunt-spritesmith](https://github.com/Ensighten/grunt-spritesmith)를 도입함.
특정 폴더에 Sprite로 만들 이미지를 넣어놓고 다음 Grunt 설정을 추가한다.

```javascript
{
  options: {
    cssFormat: 'stylus'
  },
  common: {
    src: 'web/public/img/sprites/common/*.png',
    dest: 'web/public/img/common/icons.png',
    destCss: 'web/public/css/common-icons-sprites.styl',
    cssSpritesheetName: 'common-spritesheet'
  }
}
```

`src`는 사용할 이미지 소스이고 `dest`는 sprite 이미지의 위치이다. 결과 CSS는 Stylus를 사용하므로
Stylus 파일로 지정했다. 종류별로 여러 CSS 스프라이트를 사용하기 위해서 `cssSpritesheetName`를
지정했다. `grunt sprite`로 실행하면 각 파일마다 다음과 같은 변수가 자동으로 생성된다.

```less
$icon_volo_name = 'icon-volo';
$icon_volo_x = 0px;
$icon_volo_y = 0px;
$icon_volo_offset_x = 0px;
$icon_volo_offset_y = 0px;
$icon_volo_width = 50px;
$icon_volo_height = 50px;
$icon_volo_total_width = 170px;
$icon_volo_total_height = 142px;
$icon_volo_image = '../img/common/icons.png';
$icon_volo = 0px 0px 0px 0px 50px 50px 170px 142px '../img/common/icons.png' 'icon-volo';
```

Stylus에서는 변수를 가져다 쓸 수 있으므로 각 값에 대한 변수가 다 생성되고 다음과 같이 전체에 대한
변수도 만들어진다.

```less
$common_spritesheet_width = 170px;
$common_spritesheet_height = 142px;
$common_spritesheet_image = '../img/common/icons.png';
$common_spritesheet_sprites = $icon_datebox_clock $icon_datebox_date $icon_datebox_poi $icon_facebook $icon_footer_facebook $icon_footer_google $icon_footer_insta $icon_footer_twitter $icon_footer_volo $icon_left $icon_modify $icon_more $icon_quotemark $icon_right $icon_share_facebook $icon_share_google $icon_share_link $icon_share_twitter $icon_trash $icon_underarrow $icon_volo;
$common_spritesheet = 170px 142px '../img/common/icons.png' $common_spritesheet_sprites;
```

여기서 `$common_spritesheet_`는 Grunt 설정에서 준 이름이다.

```less
spriteWidth($sprite) {
  width: $sprite[4];
}

spriteHeight($sprite) {
  height: $sprite[5];
}

spritePosition($sprite) {
  background-position: $sprite[2] $sprite[3];
}

spriteImage($sprite) {
  background-image: url($sprite[8]);
}

sprite($sprite) {
  spriteImage($sprite)
  spritePosition($sprite)
  spriteWidth($sprite)
  spriteHeight($sprite)
}

/*
The `sprites` mixin generates identical output to the CSS template
  but can be overridden inside of Stylus

This must be run when you have at least 2 sprites.
  If run with a single sprite, then there will be reference errors.

sprites($spritesheet_sprites);
*/
sprites($sprites) {
  for $sprite in $sprites {
    $sprite_name = $sprite[9];
    .{$sprite_name} {
      sprite($sprite);
    }
  }
}
```

각 넓이나 높히 등의 CSS를 만들 수 있는 mixin도 추가되므로 필요한 코드에서 가져다가 사용할 수 있다.

```less
sprites($common_spritesheet_sprites)
```

`sprites`라는 마지막 mixin은 배열로 css코드를 만드는 기능을 하므로 전체 코드를 가지고 있는
`$common_spritesheet_sprites`를 위처럼 전달하면 모든 아이콘에 대한 CSS 클래스가 자동으로
생성된다.

```css
.icon-volo {
  background-image: url("../img/common/icons.png");
  background-position: 0 0;
  width: 50px;
  height: 50px;
}
```

그래서 위의 `$icon_volo`는 위와 같은 CSS로 만들어진다. 각 코드에서는 CSS 클래스나 클래스를 못쓰는
곳에서는 변수를 가져다가 스타일을 지정하면 되므로 다양한 상황에서 사용가능하고 아이콘 크기가 달라지거나
새로운 아이콘이 추가되어도 `grunt sprite`로 다시 생성하면 클래스나 변수값이 동일하므로 변화없이
사용할 수 있다.

별도로 이렇게 작업하려다 보니 아이콘의 파일명을 디자이너와 협의할 필요가 있어 보인다.
