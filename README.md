simple side bar program using container

 
 
In my project I want to implement slidable tool bar with page view controller. So I have implemented slidable tool bar using UICollectionView & for that I used this code

.m file
@interface SlidableTabBar : UIViewController<UIPageViewControllerDataSource,UIPageViewControllerDelegate,UICollectionViewDataSource,UICollectionViewDelegate,SlideNavigationControllerDelegate,UITableViewDataSource,UITableViewDelegate>

@property (weak, nonatomic) IBOutlet UICollectionView *tabCollectionView;



@property (weak, nonatomic) IBOutlet UIView *customView;

// attributes for page view
@property (strong, nonatomic) UIPageViewController *pageViewController;
@property (nonatomic) NSArray *viewControllerIDArr;

@end

.h file
@interface SlidableTabBar ()

// detects current value in the collection view
@property NSInteger currentRow;

// contains values to be shown in tool bar
@property NSArray *testArray;

@end

@implementation SlidableTabBar


- (void)viewDidLoad {
    
    self.pageViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"PageViewController"];
    
    self.tabCollectionView.dataSource = self;
    self.tabCollectionView.delegate = self;
    
    
    [self addChildViewController:_pageViewController];
    [super viewDidLoad];
    [self setupDefaults];
    
    // Do any additional setup after loading the view.
    [_tabCollectionView setShowsHorizontalScrollIndicator:false];
    _currentRow = 0;
    
    [self loadCollectionView];
    
}

-(void)loadCollectionView
{
    // array of labels of collection view
    self.testArray = @[@"1",@"2",@"3",@"4",@"5"];
    [_tabCollectionView reloadData];
}

// collection view data source methods
-(NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView
{
    return 1;
}

-(NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section
{
    return self.testArray.count;
}

- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView
                  cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
    //UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"tabCell" forIndexPath:indexPath];
    
    customCollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"tabCell" forIndexPath:indexPath];
    cell.nameLabel.text = [self.testArray objectAtIndex:[indexPath row]];
    cell.backgroundColor = [UIColor whiteColor];
    cell.nameLabel.textColor=[UIColor blackColor];
    cell.colorLabel.backgroundColor = [UIColor whiteColor];

  return cell;
}

// page view initialization
- (void)setupDefaults
{
    // array of view controller's restoration id
    self.viewControllerIDArr = @[@"customViewController0", @"customViewController1",@"customViewController2",@"customViewController3"];
    
    self.pageViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"PageViewController"];
    
    self.pageViewController.dataSource = self;
    self.pageViewController.delegate = self;
    UIViewController *startingViewController = [self.storyboard instantiateViewControllerWithIdentifier:self.viewControllerIDArr[0]];
    
    NSArray *viewControllers = @[startingViewController];
    
    [self.pageViewController setViewControllers:viewControllers direction:UIPageViewControllerNavigationDirectionForward animated:false completion:nil];
    
    self.pageViewController.view.frame = CGRectMake(0, 0, self.customView.frame.size.width, self.customView.frame.size.height);
    
    [self addChildViewController:_pageViewController];
    
    [self.customView addSubview:_pageViewController.view];
    
    [self.pageViewController didMoveToParentViewController:self];
}

- (UIViewController *)getViewControllerFromClass:(NSString*)ViewControllerIdentifier isNext:(BOOL)next
{
    
    
    NSString* value = ViewControllerIdentifier;
    NSInteger index = [self.viewControllerIDArr indexOfObject:[NSString stringWithFormat:@"%@",value]];
    
    if (next)
        index+=1;
    else
        index-=1;
    
    if (index < 0 || index >= self.viewControllerIDArr.count)
        return nil;
    else
        return [self.storyboard instantiateViewControllerWithIdentifier:self.viewControllerIDArr[index]];
    
}


#pragma mark - Page View Controller Data Source

- (UIViewController *)pageViewController:(UIPageViewController *)pageViewController viewControllerBeforeViewController:(UIViewController *)viewController
{
    NSString *ssss = viewController.restorationIdentifier;
    return [self getViewControllerFromClass:ssss isNext:NO];
}

- (UIViewController *)pageViewController:(UIPageViewController *)pageViewController viewControllerAfterViewController:(UIViewController *)viewController
{
    NSString *ssss = viewController.restorationIdentifier;
    return [self getViewControllerFromClass:ssss isNext:YES];
}

- (NSInteger)presentationCountForPageViewController:(UIPageViewController *)pageViewController
{
    return 0;
}

- (NSInteger)presentationIndexForPageViewController:(UIPageViewController *)pageViewController
{
    return 0;
}
