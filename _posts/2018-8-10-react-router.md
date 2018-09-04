---
layout:     post
title:      react router 4学习总结
subtitle:   
date:       2018-8-10

author:     Liangyuqi
header-img: img/post-bg-2.jpg
catalog: true
tags:
    - react
---

## react router 4

React Router V4 遵循了 React 的理念：**万物皆组件**。因此 升级之后的 Route、Link、Switch等都是一个普通的组件。

React Router被拆分成三个包：react-router,react-router-dom和react-router-native。
react-router提供核心的路由组件与函数。其余两个则提供运行环境（即浏览器与react-native）所需的特定组件。

进行网站（将会运行在浏览器环境中）构建，我们应当安装**react-router-dom**。react-router-dom暴露出react-router中暴露的对象与方法，例如<Link> <BrowserRouter>这样的 DOM 类组件。，因此你只需要安装并引用react-router-dom即可。

	npm install --save react-router-dom

### 一、路由器(Router)

Router我们可以把它看做是react路由的一个路由外层盒子，它里面的内容就是我们单页面应用的**路由**以及**路由组件**。

Router是所有路由组件共用的底层接口，一般我们的应用并不会使用这个接口，而是使用高级的路由：

- \<BrowserRouter>：使用 HTML5 提供的 history API 来保持 UI 和 URL 的同步；
- \<HashRouter>：使用 URL 的 hash (例如：window.location.hash) 来保持 UI 和 URL 的同步；
- \<MemoryRouter>：能在内存保存你 “URL” 的历史纪录(并没有对地址栏读写)；
- \<NativeRouter>：为使用React Native提供路由支持；
- \<StaticRouter>：从不会改变地址；

#### 1.历史(History)
**每个路由器都会创建一个history对象**并用其保持追踪当前location并且在有变化时对网站进行重新渲染。这个history对象保证了React Router提供的其他组件的可用性，所以其他组件必须在router内部渲染。一个React Router组件如果向父级上追溯却找不到router组件，那么这个组件将无法正常工作。

#### 2.渲染\<Router>
**路由器组件只能接受一个子元素**。基于这种限制的存在，创建一个<App>组件来渲染应用其余部分是一个有效的方法（对于服务端渲染，将应用从router组件中分离也是重要的）。

	import { BrowserRouter } from 'react-router-dom'
	ReactDOM.render((
	  <BrowserRouter>
	    <App />
	  </BrowserRouter>
	), document.getElementById('root'))
	
	
### 二、根组件\<App>
	
应用通过<App>组件定义。简化一下，我们将应用拆分成两个部分。<Header>组件包含网站的导航链接。<Main>组件则呈现其余内容。

	// App组件将会被路由器组件渲染
	const App = () => (
	  <div>
	    <Header />
	    <Main />
	  </div>
	)
	
注意：你可以按你喜欢的方式对应用布局，但是将路由与导航拆分出来对于这个入门教程会成家简单。

### 三、header Link链接
现在，我们应用需要在各个页面间切换。如果使用锚点元素（就是\<a>\</a>）实现，在每次点击时页面将被重新加载。React Router提供了<Link>组件用来避免这种状况的发生。当你点击<Link>时，URL会更新，组件会被重新渲染，但是页面不会重新加载。

	import { Link } from 'react-router-dom'
	const Header = () => (
	  <header>
	    <nav>
	      <ul>
	        <li><Link to='/'>Home</Link></li>
	        <li><Link to='/roster'>Roster</Link></li>
	        <li><Link to='/schedule'>Schedule</Link></li>
	      </ul>
	    </nav>
	  </header>
	)
	
<Link>使用'to'参数来描述需要定位的页面。它的值即可是**字符串**也可是**location对象**。如果其值为字符串将会被转换为location对象。

> location 是一个含有描述URL不同部分属性的对象：    
{ pathname: '/', search: '', hash: '',state: {} }

	<Link to={{ pathname: '/roster/7' }}>Player #7</Link>
	
注意：本例的link的pathname属性只能是绝对路径。

> \<NavLink>是\<Link> 的一个特定版本, 会在匹配上当前 URL 的时候会给已经渲染的元素添加样式参数，组件属性：
	// activeClassName选中时样式为selected
	<NavLink
	  to="/faq"
	  activeClassName="selected"
	>FAQs</NavLink>
	
	// 选中时样式为activeStyle的样式设置
	<NavLink
	  to="/faq"
	  activeStyle={{
	    fontWeight: 'bold',
	    color: 'red'
	   }}
	>FAQs</NavLink>
	
	// 当event id为奇数的时候，激活链接
	const oddEvent = (match, location) => {
	  if (!match) {
	    return false
	  }
	  const eventID = parseInt(match.params.eventID)
	  return !isNaN(eventID) && eventID % 2 === 1
	}
	
	<NavLink
	  to="/events/123"
	  isActive={oddEvent}
	>Event 123</NavLink>

### 四、main 路由(Route)
\<Route>组件是React Router中主要的结构单元。在任意位置只要匹配了URL的路径名(pathname)你就可以创建<Route>元素进行渲染。

#### 1.路径(Path)
\<Route>接受一个数为string类型的path，该值路由匹配的路径名的类型。例如：\<Route path='/roster'/>会匹配以/roster[注2]开头的路径名。在当前path参数与当前location的路径相匹配时，路由就会开始渲染React元素。若不匹配，路由不会进行任何操作。

	<Route path='/roster'/>
	// 当路径名为'/'时, path不匹配
	// 当路径名为'/roster'或'/roster/2'时, path匹配
	// 当你只想匹配'/roster'时，你需要使用"exact"参数
	// 则路由仅匹配'/roster'而不会匹配'/roster/2'
	<Route exact path='/roster'/>
	
注意：在匹配路由时，React Router只关注location的路径名。当URL如下时：

	http://www.example.com/my-projects/one?extra=false
	React Router去匹配的只是'/my-projects/one'这一部分。

#### 2.match匹配路径
path-to-regexp包用来决定route元素的path参数与当前location是否匹配。它将路径字符串编译成正则表达式，并与当前location的路径名进行匹配比较。  
当路由地址匹配成功后，会创建一个含有以下属性的**match对象**：

	url ：与当前location路径名所匹配部分   
	path ：路由的地址.  
	isExact ：path是否全匹配pathname
	params ：从path-to-regexp获取的路径中取出的值都被包含在这个对象中


注意：本例中路由路径仅支持绝对路径。

#### 3.创建你的路由
可以在路由器(router)组件中的任意位置创建多个<Route>，但通常我们会把它们放在同一个位置。使用**<Switch>组件来包裹一组<Route>**。**<Switch>会遍历自身的子元素（即路由）并对第一个匹配当前路径的元素进行渲染**。

比方对于一个网站，我们希望匹配一下路径：

	/ ： 主页      
	/roster ： 团体列表    
	/roster/:number ：运动员页面，使用运动员的编号作为标识.    
	/schedule ：团队的赛程表
	
为了在应用中能匹配路径，在创建<Route>元素时必须带有需要匹配的path作为参数。

	<Switch>
	  <Route exact path='/' component={Home}/>
	  {/* 路由/roster和/roster/:number都会匹配到/roster */}
	  <Route path='/roster' component={Roster}/>
	  <Route path='/schedule' component={Schedule}/>
	</Switch>
	
> \<Route>组件有如下属性：   
- path（string）: 路由匹配路径。（没有path属性的Route 总是会 匹配）；   
- exact（bool）：为true时，则要求路径与location.pathname必须完全匹配；    
- strict（bool）：true的时候，有结尾斜线的路径只能匹配有斜线的location.pathname；   
	
#### 4.<Route>是如何渲染的？
当一个路由的path匹配成功后，路由用来确定渲染结果的参数有三种。只需要提供其中一个即可。

> component ： 一个**React组件**。当带有component参数的route匹配成功后，route会返回一个新的元素，其为component参数所对应的React组件（使用React.createElement创建）。

> render ： 一个**返回React element的函数**。当匹配成功后调用该函数。该过程与传入component参数类似，并且对于行级渲染与需要向元素**传入额外参数**的操作会更有用。

> children ： 一个返回**React element的函数**。与上述两个参数不同，**无论route是否匹配当前location，其都会被渲染**。

	<Route path='/page' component={Page} />
	
	const extraProps = { color: 'red' }
	<Route path='/page' render={(props) => (
	  <Page {...props} data={extraProps}/>
	)}/>
	
	<Route path='/page' children={(props) => (
	  props.match
	    ? <Page {...props}/>
	    : <EmptyPage {...props}/>
	)}/>
	
通常component参数与render参数被更经常地使用。children参数偶尔会被使用，它更常用在path无法匹配时呈现的'空'状态。在本例中并不会有额外的状态，所以我们将使用\<Route>的component参数。

通过\<Route>渲染的元素会被传入一些参数。分别是match对象，当前location对象以及history对象（由router创建）。

#### 5.<Main>
现在我们清楚了根路由的结构，我们需要实际渲染我们的路由。对于这个应用，我们将会在<Main>组件中渲染\<Switch>与\<Route>，这一过程会将route匹配生成的HTML放在<main>节点中。

	import { Switch, Route } from 'react-router-dom'
	const Main = () => (
	  <main>
	    <Switch>
	      <Route exact path='/' component={Home}/>
	      <Route path='/roster' component={Roster}/>
	      <Route path='/schedule' component={Schedule}/>
	    </Switch>
	  </main>
	)
	
注意：主页路由包含额外参数。该参数用来保证路由能准确匹配path。

#### 6.嵌套路由
运动员路由/roster/:number并未包含在上述<Switch>中。它由<Roster>组件负责在路径包含'/roster'的情形下进行渲染。

在\<Roster>组件中，我们将为两种路径进行渲染：

/roster ：对应路径名仅仅是/roster时，因此需要在exact元素上添加exact参数。

/roster/:number ： 该路由使用一个路由参数来获取/roster后的路径名。

	const Roster = () => (
	<Switch>
	
	<Route exact path='/roster' component={FullRoster}/>
	<Route path='/roster/:number' component={Player}/>
	</Switch>
	)

组合在相同组件中分享共同前缀的路由是一种有用的方法。这就需要简化父路由并且提供一个区域来渲染具有相同前缀的通用路由。

例如，\<Roster>用来渲染所有以/roster开始的全部路由。

	const Roster = () => (
	  <div>
	    <h2>This is a roster page!</h2>
	    <Switch>
	      <Route exact path='/roster' component={FullRoster}/>
	      <Route path='/roster/:number' component={Player}/>
	    </Switch>
	  </div>
	)
	
#### 7.路径参数
有时路径名中存在我们需要获取的参数。例如，在运动员界面，我们需要获取运动员的编号。我们可以向route的路径字符串中添加path参数

如'/roster/:number'中:number这种写法意味着/roster/后的路径名将会被获取并存在match.params.number中。例如，路径名'/roster/6'会获取到一个对象：

{ number: '6' } // 注获取的值是字符串类型的
\<Player>组件可以使用props.match.params对象来确定需要被渲染的运动员的数据。

	// 返回运动员对象的API
		import PlayerAPI from './PlayerAPI'
		const Player = (props) => {
		  const player = PlayerAPI.get(
		    parseInt(props.match.params.number, 10)
		  )
		  if (!player) {
		    return <div>Sorry, but the player was not found</div>
		  }
		  return (
		    <div>
		      <h1>{player.name} (#{player.number})</h1>
		      <h2>{player.position}</h2>
		    </div>
		)


除了\<Player>组件，我们的页面还包含\<FullRoster>, \<Schedule>以及 \<Home>组件。

	const FullRoster = () => (
	  <div>
	    <ul>
	      {
	        PlayerAPI.all().map(p => (
	          <li key={p.number}>
	            <Link to={`/roster/${p.number}`}>{p.name}</Link>
	          </li>
	        ))
	      }
	    </ul>
	  </div>
	)
	const Schedule = () => (
	  <div>
	    <ul>
	      <li>6/5 @ Evergreens</li>
	      <li>6/8 vs Kickers</li>
	      <li>6/14 @ United</li>
	    </ul>
	  </div>
	)
	const Home = () => (
	  <div>
	    <h1>Welcome to the Tornadoes Website!</h1>
	  </div>
	)
