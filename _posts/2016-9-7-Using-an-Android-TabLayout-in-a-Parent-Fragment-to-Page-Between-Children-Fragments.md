---
layout: post
title: Using an Android TabLayout in a Parent Fragment to Page Between Children Fragments
categories: [snippets]
tags: [Android, XML, Java]
description: It's pretty easy to find tutorials on how to use a `TabLayout` within an activity, as well as to find those that page through a bunch of views. I had difficulty finding a proper explanation for my use case - how to use a `TabLayout` within a fragment, which in turn would host other fragments as the user scrolled between tabs.
---
Android Tutorial: Using an Android TabLayout in a Parent Fragment to Switch Between Child Fragments

It's pretty easy to find tutorials on how to use a `TabLayout` within an activity, as well as to find those that page through a bunch of views. I had difficulty finding a proper explanation for my use case - how to use a `TabLayout` within a fragment, which in turn would host other fragments as the user scrolled between tabs. It's not difficult, but I found it extremely confusing as a beginner Android programmer.

I'm going to assume that how to use the fragment itself is already known - I'll only be talking about constructing the fragment, not displaying it in an activity and whatnot.

Complete code is located at the bottom, or you can follow along step-by-step with some light explanation.

***

* Define your host fragment's layout.

    - As always, we need to define a layout before we can do anything else. We'll need to define a layout with a `TabLayout` and a `ViewPager` as child elements.
  
    {% highlight xml %}
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:app="http://schemas.android.com/apk/res-auto"
	    android:layout_width="fill_parent"
	    android:layout_height="fill_parent"
	    android:background="#3B3B3B"
	    android:orientation="vertical">
	    
	    <android.support.design.widget.TabLayout
	        android:id="@+id/tabs"
	        style="@style/Empyrean.TabLayout"
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        app:tabGravity="fill"
	        app:tabMode="fixed" />

	    <android.support.v4.view.ViewPager
	        android:id="@+id/viewpager"
	        android:layout_width="match_parent"
	        android:layout_height="0dp"
	        android:layout_weight="1" />
    </LinearLayout>
    {% endhighlight %}  


<br>
    
* Create your host fragment.

    - We're going to define a fragment in typical fashion, overriding `onCreateView` and `onDestroyView`. Easy enough, right?

    {% highlight java %}
	public class HostFragment extends Fragment {
	
	    @Override
	    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
	        view = inflater.inflate(R.layout.host_fragment_layout, container, false);
	        super.onCreate(savedInstanceState);
	    }
	
	    @Override
	    public void onDestroyView() {
	        super.onDestroyView();
	    }
	}
    {% endhighlight %}  


<br>
      
*  Create your child fragments. 

    - Again, we're going to define a fragment as usual. We're only going to define two basically blank fragments with different background colors, just to illustrate the switching between them.

    - The Java code for the fragments:

    {% highlight java %}
	public class ChildFragment1 extends Fragment {
	    
	    private View view;

	    @Nullable
	    @Override
	    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
	        view = inflater.inflate(R.layout.child_fragment1_layout, null);
	        return view;
	    }
	}
    {% endhighlight %}
    {% highlight java %}
	public class ChildFragment2 extends Fragment {
	
	    private View view;
	
	    @Nullable
	    @Override
	    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
	        view = inflater.inflate(R.layout.child_fragment2_layout, null);
	        return view;
	    }
	}
    {% endhighlight %}

    {% highlight xml %}
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:background="#FAA"
	    android:orientation="horizontal" >
	
	    <TextView
	        android:id="@+id/child_fragment1_layout"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:layout_gravity="center_vertical"
	        android:text="fragment 1" />
	
	</LinearLayout>
    {% endhighlight %}
    {% highlight xml %}
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:background="#BCA"
	    android:orientation="horizontal" >
	
	    <TextView
	        android:id="@+id/child_fragment2_layout"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:layout_gravity="center_vertical"
	        android:text="fragment2" />
	
	</LinearLayout>
    {% endhighlight %}  


<br>

* Define an adapter.

    - Now we're into the meat of the subject...we currently have a HostFragment with a TabLayout that controls a `ViewPager`, and we want our ViewPager to cycle through the two child fragments we've defined. We can let our `ViewPager` know how to do that with an `Adapter`. More specifically, since we want to cycle through fragments, we're going to create a custom `FragmentPagerAdapter`.

    {% highlight java %}
	 private class Adapter extends FragmentPagerAdapter {
	        private final List<Fragment> mFragments = new ArrayList<>();
	
	        public Adapter(android.support.v4.app.FragmentManager fm) {
	            super(fm);
	        }
	
	        public void addFragment(Fragment fragment) {
	            mFragments.add(fragment);
	        }
	
	        @Override
	        public Fragment getItem(int position) {
	            return mFragments.get(position);
	        }
	
	        @Override
	        public int getCount() {
	            return mFragments.size();
	        }
	    }
    {% endhighlight %}  
    
    
<br>

* Define the way we're going to set up our ViewPager inside of HostFragment

    - Declare an instance of the adapter (passing in a ChildFragmentManager) that we just defined in `HostFragment`, and then declare the function setupViewPager. 
  
    - Add your two child fragments to the adapter, and then set our let our `ViewPager` know that we want to use our new adapter, along with the two fragments it contains.

    - In our Your HostFragment class should end up looking something like..

    {% highlight java %}
	public class HostFragment extends Fragment {
		private Adapter adapter;
	
		private void setupViewPager(ViewPager viewPager) {
		        adapter = new Adapter(getChildFragmentManager());
		        adapter.addFragment(new ChildFragment1());
		        adapter.addFragment(new ChildFragment2());
		        viewPager.setAdapter(adapter);
		    }
	}
    {% endhighlight %}

* Almost there! Lastly, we need to use our `setupViewPager` function to actually setup the `ViewPager` in `HostFragment`. 

    - In your `onCreateView` method, add the following lines:

    {% highlight java %}
	    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
	
	        view = inflater.inflate(R.layout.ui_fragment, container, false);
	        super.onCreate(savedInstanceState);
	       
	        // new lines
	        // [1]
	        final ViewPager viewPager = (ViewPager) view.findViewById(R.id.viewpager);
	        setupViewPager(viewPager);
	        TabLayout tabLayout = ButterKnife.findById(view, R.id.tabs);
	        tabLayout.setupWithViewPager(viewPager);
	
	        return view;
	    }
    {% endhighlight %}

    - All [1] is doing is telling our `ViewPager` to use the `setupViewPager` function, in which we defined what fragments our adapter would use in the previous step

    - [2] is simply telling our tabLayout to also follow along with our ViewPager - both of them should change when you swipe.

  
