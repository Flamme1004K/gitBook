# 소프트링크와 하드링크의 차이

소프트 링크\(soft link, symbolic link\)

- 원본파일을 연결하는 효과를 가지며, 파일이나 디렉터리에 모두 사용이 가능하다.

- 원본 파일이 삭제되면 소프트링크를 사용할 수 없다. 

    -&gt; 소프트 링크가 생성 될때 원본 파일에 대한 포인터만 포함되므로 새로운 inode를 사진 링크 파일이 생성되기 때문.

하드 링크\(hard link\)

- 하나의 동일한 파일을 디스크의 다른 곳에 배치\(복사와 같은 형태\)하여 여러 이름으로 사용되는 형식이며, 파일에만 부여할 수 있음.

- 원본 파일을 직접적으로 가리키고 있기 때문에 원본 파일을 수정하여도 하드링크 파일이 수정, 하드링크 파일을 수정하여도 원본 파일에 반영

- 원본 파일이 삭제 되어도 하드 링크 파일은 삭제되지 않기 때문에 데이터 손실이 일어나지 않는다.

    -&gt; indoe번호를 복사했기 때문에 원본 파일을 삭제하여도 데이터를 볼 수 있다. 

    -&gt; 원본 파일이 삭제되도 meta data가 삭제되지 않기 때문. 하지만 그 다음 하드링크 파일까지 삭제하면 inode가 삭제되며 meta data도 삭제 된다.
