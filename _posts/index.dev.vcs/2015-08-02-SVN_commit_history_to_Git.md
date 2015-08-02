SVN commit history를 Git로 옮기기
===============================

개요
----

svn에서 git로 SCM을 바꾸게 되었을 때 가장 고민되는 게 commit history를
그대로 옮기는 부분이다. svn서버에서 제대로 지원만 해준다면 안될 부분은
없다. 이미 git command로 잘 지원해주고 있기 때문이다.

방법
----

<http://stackoverflow.com/questions/9211405/how-to-migrate-code-from-svn-to-git-without-losing-commits-history>
<http://git-scm.com/book/en/Git-and-Other-Systems-Migrating-to-Git>

위 URL들을 참고하여 git svn clone을 이용해서 git local repository를
만들고 remote url을 추가하는 식으로 처리하면 된다. 다만, svn서버가 특정
시간내에 너무 많은 요청을 보냈을 때 timeout을 낸다거나 요청을 거부할
수가 있다. 이 경우는 option을 최대한 걸어서 최대한 데이터를 적게
요청하도록 해서 중요한 history만 가져오도록 한다.
