ͼƬ��������ʵ�ַ������ɸ������ּ�ǿ�Ķ�������Ȼ��ѡ��ʹ��ͼƬ���ؿ��������ʵ�֡�
����Android-Universal-Image-Loader��Glide��Picasso��Fresco֮�࣬ ������ʱ��ķ������ڣ���ô��ͼƬ���ؿ�ܵ���˭��ʵ�ã�

 1. ��˵Fresco����Ϊ֧��WebP����������NDK������ͼƬ������JavaHeap��ʹ��
 2. ��Picasso������Ч
 3. ��˵Glide��Picasso���������Լ���Gif����Picasso��������չ�˺ܶ෽��
 4. ImageLoader  ʹ����㣬��Ϊ�������磬��ϧû��ά����
 
���ˣ��Ͼ�**ʵ�����Ǽ��������Ψһ��׼**�������Լ�д��������£����û���ѧϰ�¸��������ôʹ�õġ�
���ĵ���׼����ʲô��һͼʤǧ��
![����дͼƬ����](http://img.blog.csdn.net/20160706152847155)
#�غ�һ����������ͼƬ�����ٶ�

##��λ�ȡͼƬ��������ʱ��
###**�����޸Ŀ�ܴ���**
�ҿ�ʼ���ԣ��޸�ͼƬ���ؿ�ܵĴ��룬�ڳ�ʼ����ʱ���¼��ϵͳʱ��t1��Ȼ��ͼƬ���سɹ���ʹ�õ�ǰϵͳʱ��t2,t2-t1���ǿ�ܼ��������ĵ�ʱ�䡣����ʵ��ȥ����ʱ���ڸ���PicassoԴ��󣬷�������ʵ�ֿ�����̫�����൱��ÿ������Ҷ���Ҫȥ�޸Ĵ��룬���Һ���Ŀ��ʵ�ֶ���PicassoҪ���ӣ���Fresco���֣����뷱�࣬�޸��������鷳��
###**���ÿ�ܱ�������-ռλͼ�ʹ�����ʾͼ**
��һ��˼·�����еĿ�ܲ���֧��ռλ�����ͳ��������Ҳ������ͼƬ���ع����п���������һ��ռλͼƬ��������س�����Է���һ�ų���ͼƬ��

```
        Glide.with(getContext())
                .load(url)
                .placeholder(Drawables.sPlaceholderDrawable) //��ʼ����ʾ
                .error(Drawables.sErrorDrawable)  //����ʧ����ʾ
                .into(mImageView);
```
Ҳ����˵���ǿ��Ը��ݣ����ռ��س�����ͼƬ��Դ���ж����سɹ����
![����дͼƬ����](http://img.blog.csdn.net/20160705185255391)��
��ô��ô������ʱ�䣿
ͼƬ������ʾ��ImageView�ϣ����Ǵ��ĸ���ܵ�����`Drawable`���͵�ͼ����ô������տ϶������ImageView��`setImageDrawable()`����������ͨ����дImageView�������������ʵ�֣���Ҫ��` public void setImageDrawable(Drawable drawable)`����Ĵ���

```
public class WatchImageView extends ImageView implements WatchInterface {
    private final WatchImpl mWatcher;

    public WatchImageView(Context context) {
        super(context);
        //����ͼƬ�����ʽΪ�����������ؼ�
        setScaleType(ScaleType.CENTER_CROP);
        mWatcher = new WatchImpl(this);
    }


    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        //���ƽ�����õ���ͨ��mWatcherת��
        mWatcher.onDraw(canvas);
    }

//    ���������������ֹ���ʹ������ʾͼƬ��Ӱ�����ǲ�����ʾ
    @Override
    public void setImageURI(Uri uri) {
        throw new UnsupportedOperationException();
    }

    @Override
    public void initWatcher(String tag, WatchListener watchListener) {
        mWatcher.init(tag, watchListener);
        mWatcher.onStart(); //��ʼ��
    }
    
//����ʵ��
    @Override
    public void setImageDrawable(Drawable drawable) {

        Preconditions.checkNotNull(drawable);
        if (drawable == Drawables.sPlaceholderDrawable) {
            //����������ã��ڳ�ʼ����������Glide.build()ʵ�ֵ��ø���
        } else if (drawable == Drawables.sErrorDrawable) {
            mWatcher.onFailure();//����ʧ��
        } else {
            mWatcher.onSuccess();//���سɹ�
        }
        super.setImageDrawable(drawable);
    }
}
```

�����WatchInterface������ʼ������������

```
public interface WatchInterface {
    void initWatcher(final String tag, WatchListener watchListener);
}
```
�������ͳ�Ʒ���`WatchListener `��ʵ�֡�
###**ע�⣺һ��Ҫ����ռλͼ�ͳ���ͼ**
��Ϊ����������ͨ���жϣ���ǰImageView��ʾ�ľ�������һ��Drawable���ж�����ʧ�ܣ����سɹ�������ȡ�����ء�����һ��Ҫ���ã������޶�ռλͼһ��Ҫ��`R.drawable.placeholder`
![����дͼƬ����](http://img.blog.csdn.net/20160706002941609)
����ͼһ��Ҫ��`R.drawable.error`
![����дͼƬ����](http://img.blog.csdn.net/20160706003015094)
Ϊ�˷���ʹ�ã������˼򵥷�װ

```
public class Drawables {
    public static Drawable sPlaceholderDrawable;
    public static Drawable sErrorDrawable;

    private Drawables() {
    }

    public static void init(final Resources resources) {
        if (sPlaceholderDrawable == null) {
            sPlaceholderDrawable = resources.getDrawable(R.drawable.placeholder);
        }
        if (sErrorDrawable == null) {
            sErrorDrawable = resources.getDrawable(R.drawable.error);
        }
    }
}
```
ʹ�ã�

 1.Glide
 

```
        Glide.with(getContext())
                .load(url)
                .placeholder(Drawables.sPlaceholderDrawable)
                .error(Drawables.sErrorDrawable)
```

2.Picasso  ��ͬС��

```
        mPicasso.load(url)
                .placeholder(Drawables.sPlaceholderDrawable)
                .error(Drawables.sErrorDrawable)
```
3.ImageLoader

```
            mImageOptions = new DisplayImageOptions.Builder()
                    .showImageOnLoading(Drawables.sPlaceholderDrawable)
                    .showImageOnFail(Drawables.sErrorDrawable)
```
4.Fresco

```
        GenericDraweeHierarchy genericDraweeHierarchy = new GenericDraweeHierarchyBuilder(getContext().getResources())
                .setPlaceholderImage(Drawables.sPlaceholderDrawable)
                .setFailureImage(Drawables.sErrorDrawable)
```
##����ע������
Ϊ�˱�֤�����ԵĹ�ƽ�ԣ���֤���ʹ�õļ��ػ���һ�£���ÿ�����������˵

 1. ����ͼƬ�ĵ�ַһ�£�����˳��һ��
 2. ��ֹʹ���ڴ滺�棬Ӳ�̻��棬ֻ��ͨ�������ȡͼƬ
 3. ���Ե���Ӳ������һ��,��:ʹ��ͬһ���ֻ����ԣ�����ÿ��ʹ��һ����ܼ��غ�����ڴ���ʹ����һ����ܲ���

##���ò�ʹ�û���
��Ϊ�ǲ�������ͼƬ�����������ڴ滺��ʹ��̻���Ӧ�ñ���ֹ
###**Glide��ʹ�û���**
ֱ���ṩ��
```
                .skipMemoryCache(true) //��ʹ���ڴ滺��
                .diskCacheStrategy(DiskCacheStrategy.NONE) //��ʹ��Ӳ�̻���
```

###**Fresco��ʹ�û���**
Fresco�鷳һ�㣬���˺þ�û�ҵ����������**�����֪�������ѣ������������**
��һ��˼·����ֱ�����������ڴ滺��Ϊ�ռ�Ϊ0�����̻���ռ�ҲΪ0������


###**Picasco��ʹ�û���**
һ�仰�򵥿��
```
                .memoryPolicy(MemoryPolicy.NO_CACHE, MemoryPolicy.NO_STORE)//��ʹ���ڴ滺��
                .networkPolicy(NetworkPolicy.NO_CACHE, NetworkPolicy.NO_STORE)//��ʹ�ô��̻���
```
###**ImageLoader��ʹ�û���**

```
                    .cacheInMemory(false) //��ʹ���ڴ滺��
                    .cacheOnDisk(false)  //��ʹ��Ӳ�̻���
```

 
##����Ч��
�������绷���£�������ܱ��ֶ���ࣨ��Ҳ����ʹ�ñ��ĵ�Demo��Ӧ�ñ�����ٶȿ���ˣ����ڼ�����Ի���һ��ͼƬ��1000-2000ms���ҡ���
���ǣ��������ʹ�õ�����Ƚ���
|����| ͳ�ƴ���| ƽ��ʱ��(ms)  |
| :------------: |:-------------:| :-----:|
| Fresco| 72 | 3877ms |
|  Picasso | 100|   3976ms |
| ImageLoader | 100|    4402ms |
| Glide | 100|    4800ms |
����������ϣ�Glide��Ȼ������
###**Fresco��Native Heap**
�ǵ�ǰ�棬���ǰ�Ӳ�̻�����ڴ滺��Ĵ�С������Ϊ0������Fresco����һ��Native�㻺�档
���ֲ��裺

 1. Fresco��������ͼƬ���˳�App
 2. �ر����磬����App��̨ռ����Դ
 3. �ٴδ򿪣�ʹ��Fresco�ٴμ��أ���Ȼ����һЩż���ļ��سɹ�
![����дͼƬ����](http://img.blog.csdn.net/20160706163333305)
������ʱ��73ms�����������Ի���
###**Glide��������ʧ��**
������������ʱ�����������ټ��������л������粻��ô�ã����Glide����ʧ�ܵ����Ƶ����
��ϸ�۲����ʱ�䣬�������2645ms�ͻص���ʾʧ�ܣ��ؼ���ͬʱ���ص�ͼƬ�У����д���10000+ms���سɹ���������������Ҿ��÷ǳ�����
 ![����дͼƬ����](http://img.blog.csdn.net/20160706141835536)


Glide��ʹ�ñ���������ص�ʱ�������绷�����õ�ʱ�򣬼����ٶ��������ز������⡣����������Glide������OkHttp��ΪHttpClient���������������������ں����Blogд��������



 
#�غ϶�����������ٶ�

##ǰ��

 1. ��֤ÿ�����ʹ�õ��ڴ滺�棬���̻��涼��һ�µġ����ڴ滺��Ϊϵͳ���Ի����1/4�����̻��涼��50M��
 2. ��֤ͼƬ���ѻ��浽���أ��ı�ͼƬ����˳�������������
 3. ���Ի�������һ��
 
##�޸Ļ����С
�Ȱ�������Ϣд���ļ�

```
public class ConfigConstants {
    public static final int MAX_HEAP_SIZE = (int) Runtime.getRuntime().maxMemory();
    public static final int MAX_CACHE_MEMORY_SIZE = MAX_HEAP_SIZE / 4;
    public static final int MAX_CACHE_DISK_SIZE = 50 * 1024 * 1024;
}
```

###Glide
Glide�޸�������Ϣ�ķ�ʽ����ͨ��XML���Ԫ��
����
```
public class GlideConfigModule implements GlideModule {
    public final static String TAG = "GlideConfigModule";

    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        builder.setDiskCache(new InternalCacheDiskCacheFactory(context, ConfigConstants.MAX_CACHE_DISK_SIZE));
        builder.setMemoryCache(new LruResourceCache(ConfigConstants.MAX_CACHE_MEMORY_SIZE));
        builder.setBitmapPool(new LruBitmapPool(ConfigConstants.MAX_CACHE_MEMORY_SIZE));
    }

    @Override
    public void registerComponents(Context context, Glide glide) {
    }
}
```
Ȼ����AndroidManifest�ļ���

```
        <meta-data
            android:name="com.bumptech.glide.integration.okhttp3.OkHttpGlideModule"
            android:value="GlideModule"/>
```
����һ����֤��ȫ·��
###Picasso
Picassoͨ��`Picasso.Builder`�����ã��Ͼ�Builder�����Square��˾һ��ķ����
```
public class PicassoConfigFactory {
    private static Picasso sPicasso;

    public static Picasso getPicasso(Context context) {
        if (sPicasso == null) {
            sPicasso = new Picasso.Builder(context)
                    //Ӳ�̻���ش�С
                    .downloader(new OkHttpDownloader(context, ConfigConstants.MAX_CACHE_DISK_SIZE))
                    //�ڴ滺��ش�С
                    .memoryCache(new LruCache(ConfigConstants.MAX_CACHE_MEMORY_SIZE))
                    .build();
        }
        return sPicasso;
    }
}
```

###ImageLoader
ImageLoader��Picasso����
```
public class ImageLoaderFactory {
    private static ImageLoader sImageLoader;
    public static ImageLoader getImageLoader(Context context) {

        if (sImageLoader == null) {
            ImageLoaderConfiguration imageLoaderConfiguration = new ImageLoaderConfiguration.Builder(context)
                    .diskCacheSize(ConfigConstants.MAX_CACHE_DISK_SIZE)
                    .memoryCacheSize(ConfigConstants.MAX_CACHE_MEMORY_SIZE)
                    .build();

            sImageLoader = ImageLoader.getInstance();
            sImageLoader.init(imageLoaderConfiguration);
        }
        return sImageLoader;
    }
}
```

###Fresco
Frescoʹ��`ImagePipelineConfig`��ʵ��
```
    public static ImagePipelineConfig getImagePipelineConfig(Context context) {
        if (sImagePipelineConfig == null) {
            sImagePipelineConfig = ImagePipelineConfig.newBuilder(context)
                    .setMainDiskCacheConfig(DiskCacheConfig.newBuilder(context)
                            .setMaxCacheSize(ConfigConstants.MAX_CACHE_DISK_SIZE)
                            .build())
                    .setBitmapMemoryCacheParamsSupplier(
                            new Supplier<MemoryCacheParams>() {
                                @Override
                                public MemoryCacheParams get() {
                                    return new MemoryCacheParams(ConfigConstants.MAX_CACHE_MEMORY_SIZE,
                                            Integer.MAX_VALUE,
                                            Integer.MAX_VALUE,
                                            Integer.MAX_VALUE,
                                            Integer.MAX_VALUE);
                                }
                            }
                    )
                    .build();
        }
        return sImagePipelineConfig;
    }
}
```
Ȼ����Fresco��ʼ�����º�����ȥ

```
 Fresco.initialize(context, FrescoConfigFactory.getImagePipelineConfig(context));
```
##ͼƬ�������

```
    public static final String[] URLS = {
            BASE + "CqmBjo5" + EXT, BASE + "zkaAooq" + EXT, BASE + "0gqnEaY" + EXT,
            BASE + "9gbQ7YR" + EXT, BASE + "aFhEEby" + EXT, BASE + "0E2tgV7" + EXT,
            BASE + "P5JLfjk" + EXT, BASE + "nz67a4F" + EXT, BASE + "dFH34N5" + EXT,
            BASE + "FI49ftb" + EXT, BASE + "DvpvklR" + EXT, BASE + "DNKnbG8" + EXT,
            BASE + "yAdbrLp" + EXT, BASE + "55w5Km7" + EXT, BASE + "NIwNTMR" + EXT,
            BASE + "DAl0KB8" + EXT, BASE + "xZLIYFV" + EXT, BASE + "HvTyeh3" + EXT,
            BASE + "Ig9oHCM" + EXT, BASE + "7GUv9qa" + EXT, BASE + "i5vXmXp" + EXT,
            BASE + "glyvuXg" + EXT, BASE + "u6JF6JZ" + EXT, BASE + "ExwR7ap" + EXT,
            BASE + "Q54zMKT" + EXT, BASE + "9t6hLbm" + EXT, BASE + "F8n3Ic6" + EXT,
            BASE + "P5ZRSvT" + EXT, BASE + "jbemFzr" + EXT, BASE + "8B7haIK" + EXT,
            BASE + "aSeTYQr" + EXT, BASE + "OKvWoTh" + EXT, BASE + "zD3gT4Z" + EXT,
            BASE + "z77CaIt" + EXT,
    };
    public void setRandomDatas() {
        Collections.addAll(mDatas, Data.URLS);
        Collections.shuffle(mDatas);
        List<String> copyDatas = new ArrayList<>(mDatas);
        mDatas.addAll(copyDatas);
        mDatas.addAll(copyDatas);
    }
```
##���ս��
�����������ͼƬ����֤ͼƬ�Ѿ�ȫ�����ص����ء�
|����| ͳ�ƴ���| ƽ��ʱ��(ms)  |
| :------------: |:-------------:| :-----:|
| ImageLoader |  34|    57ms |
| Glide |  34|    77ms |
| Fresco| 34 | 91ms |
|  Picasso |  34|   278ms |

Glide��
![����дͼƬ����](http://img.blog.csdn.net/20160706172532731)
Picasso��
![����дͼƬ����](http://img.blog.csdn.net/20160706172607278)

 ����Picasso����������һ����Σ��ر����ڿ��ٻ�����ʱ�򣬿��ǵ�Picassoʹ�õ���`Bitmap.Config.ARGB_8888`���ܻ����ĸ����ڴ��GPU��Դ�����ǰ����޸�Ϊ`Bitmap.Config.ARGB_4444`����һ��
 ![����дͼƬ����](http://img.blog.csdn.net/20160706175433693)
 
ռ�õ��ڴ��ȷ��60+M���͵�37M�����ǵ�ͼƬ�������Ҳ�����������ȷ�ܴ�
���ǣ�ƽ������ʱ�仹��279ms��
�²�����ܺ�Picasso�Ļ������ѡ�񻺴�ԴͼƬ��С�����������Ĭ��ѡ�񻺴���ӦImageView��ĳߴ��ԭ����

�������ص�ַ��https://github.com/zhouruikevin/ImageLoadPK