안드로이드 개발을 하다보면 수많은 Activity와 Fragment를 생성하게 된다.

그런데 액티비티와 프래그먼트가 많아질수록 ViewBinding 초기 설정하는 것도 만만치 않다.

```
private lateinit var binding:Activity~Binding
binding=~.
setContentView(binding.root)
```

이걸 언제 하나하나 다 하고 있냐....



> Base 추상클래스로 쉽게 초기 설정을 해보자👀  



**🍯 ① BaseActivity 적용하기**

**BaseActivty.kt**

```
abstract class BaseActivity<B: ViewBinding>(
    val bindingFactory: (LayoutInflater) -> B
): AppCompatActivity() {

    private var _binding: B? = null
    val binding get() = _binding!!

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        _binding = bindingFactory(layoutInflater)
        setContentView(binding.root)
    }

    override fun onDestroy() {
        super.onDestroy()
        _binding = null
    }
}
```

\-> 실제 Activity에 적용한 예시

HomeActivity.kt

```
class HomeActivity : BaseActivity<ActivityHomeBinding>({ ActivityHomeBinding.inflate(it)}) {

    private lateinit var homeViewPagerAdapter: HomeViewPagerAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        initViewPagerAdapter()
        initBottomNavigation()
    }
```

**🍯② BaseFragment 적용하기**

**BaseFragment.kt**

```
abstract class BaseFragment<B: ViewBinding>: Fragment() {

    private var _binding: B? = null
    val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = getFragmentBinding(inflater, container)
        return binding.root
    }

    abstract fun getFragmentBinding(inflater: LayoutInflater, container: ViewGroup?): B

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

\-> 실제 Fragment에 적용한 예시

HomeFollowerFragment.kt

```
class HomeFollowerFragment : BaseFragment<FragmentHomeFollowerBinding>() {

    override fun getFragmentBinding(
        inflater: LayoutInflater,
        container: ViewGroup?
    ): FragmentHomeFollowerBinding {
        return FragmentHomeFollowerBinding.inflate(inflater,container,false)
    }

}
```