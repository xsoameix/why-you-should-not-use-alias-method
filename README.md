有段別人寫好的套件，裡頭的程式碼長這樣：

    require 'active_support/all'
    class Origin
    
      def la
        puts 1
      end
    end

然而這個程式碼卻有個 bug，你等不及作者把他修好，便加了這段程式碼把原本的 la 的行為改掉

    class Origin
    
      def la2
        puts 2
        la1
      end
      alias_method :la1, :la
      alias_method :la, :la2
    end

然後你執行

    > Origin.new.la
    2
    1

很正常！修掉 bug 了

但是，你裝的另外一個套件也發現了同一個 function 裡有 bug (可以是不同 bug)，然後也等不及這個套件的作者修好，便加了這段程式碼：

    class Origin
    
      def la3
        puts 3
        la1
      end
      alias_method :la1, :la
      alias_method :la, :la3
    end

    > Origin.new.la
    stack level too deep (SystemStackError)

然後，你的程式碼就爆了

如果你和另外那個套件用的是 prepend 的話...

    # 你寫的
    module Patch1
    
      def la
        puts 2
        super
      end
    end
    class Origin
    
      prepend Patch1
    end

    # 那個套件寫的
    module Patch2
    
      def la
        puts 3
        super
      end
    end
    class Origin
    
      prepend Patch2
    end

不但沒爆，還解決了兩個 bug (你解決的加上那個套件解決的)
