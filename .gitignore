//
//  InstagramViewController.m
//  InstagramAPP
//
//  Created by ohida sultana on 7/28/15.
//  Copyright (c) 2015 WorldPress. All rights reserved.
//

#import "InstagramViewController.h"
#import "AFNetworking.h"
#import "AFHTTPRequestOperation.h"
#import "UIImageView+AFNetworking.h"

@interface InstagramViewController ()

@end

@implementation InstagramViewController
@synthesize collectionView;
NSDictionary *instagramJSONDic;
UIView *fullScreenImageView;
int count = 0;


#pragma mark collectionView code

- (NSInteger)collectionView:(UICollectionView *)view numberOfItemsInSection:(NSInteger)section {
    NSArray *tempArray =[instagramJSONDic objectForKey:@"data"];
    return [tempArray count];
}

- (NSInteger)numberOfSectionsInCollectionView: (UICollectionView *)collectionView {
    return 1;
}


- (UICollectionViewCell *)collectionView:(UICollectionView *)cv cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"cell" forIndexPath:indexPath];
    
    __weak UICollectionViewCell *weakCell = cell;
    
    
    
    NSString *imageURL = [[[[[instagramJSONDic objectForKey: @"data" ] objectAtIndex:indexPath.row] objectForKey:@"images"] objectForKey:@"standard_resolution"] objectForKey:@"url"];
    //Kinda honked off at the Instagram API here. It sends me a thumbnail, and the standard image, but I can't request a larger version of the image than 640x640.
    
    UIImageView *imageView = [[UIImageView alloc] initWithFrame:CGRectMake(0, 0, cell.frame.size.width, cell.frame.size.height)];
    imageView.tag = indexPath.row;
    
    [cell addSubview:imageView];
    
    NSURL *url = [NSURL URLWithString:imageURL];
    
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    UIImage *placeholderImage = [UIImage imageNamed:@"A.png"];
    imageView.contentMode = UIViewContentModeScaleAspectFit;
    
    
    //Nice stuff that AFNetworking gives us.
    [imageView setImageWithURLRequest:request
                     placeholderImage:placeholderImage
                              success:^(NSURLRequest *request, NSHTTPURLResponse *response, UIImage *image) {
                                  
                                  
                                  [imageView setImage:image];
                                  
                                  [weakCell setNeedsLayout];
                                  
                                  
                              } failure:nil];
    
    cell.backgroundColor = [UIColor whiteColor];
    cell.layer.borderColor = [UIColor whiteColor].CGColor;
    cell.layer.borderWidth = 2;
    return cell;
}

#pragma mark - UICollectionViewDelegate
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath
{
    //This is called when you tap on an image in the collection view.
    
    //I'd probably put this a nice UIView Animation Block
    
    fullScreenImageView = [[UIView alloc] initWithFrame: CGRectMake(0, 20, [[UIScreen mainScreen] bounds].size.width, [[UIScreen mainScreen] bounds].size.height-20 )];
    
    fullScreenImageView.backgroundColor = [UIColor blackColor];
    
    //Tags are fun. In a grownup app I'd be storing the image, and not be doing this pointer nonsense on a datastructure like the collectionView that can have the item in memory disapear.
    UIImageView *fullImageView = (UIImageView *)[self.view viewWithTag: indexPath.row];
    
    UIImageView *pictureView = [[UIImageView alloc] initWithFrame:CGRectMake(40, 200, 300, 300)];
    pictureView.image =  fullImageView.image;
    
    [fullScreenImageView addSubview:pictureView];
    [self.view addSubview:fullScreenImageView];
    
    UITapGestureRecognizer* tgr = [[UITapGestureRecognizer alloc] initWithTarget: self action: @selector( onTap: )];
    [fullScreenImageView addGestureRecognizer: tgr];
    
    
}


- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout sizeForItemAtIndexPath:(NSIndexPath *)indexPath {
    
    CGSize frameSize;
    switch(count){
        case 0  :
            frameSize = CGSizeMake(125, 125);
            count =  count + 1;
            break;
        case 1  :
            frameSize = CGSizeMake(100, 100);
            count = count + 1;
            break;
        case 2 :
            frameSize = CGSizeMake(75, 75);
            count = 0;
        default :
            frameSize = CGSizeMake(75, 75);
            
    }
    [self.collectionView reloadData];
    return frameSize;
    
}

#pragma mark networking code

-(void)startAFDownload {
    //The API for accessing taged for instagram requires setting up an oAuth 2.0 client infastructure.
    //Since it wasn't required in the spec, I'm not including it, and going with a pregenerated token.
    
    NSString *urlString = @"https://api.instagram.com/v1/tags/selfie/media/recent?access_token=647785057.1677ed0.4de58c986679455d95d7a74ed8c22d53";
    NSURL *url = [NSURL URLWithString:urlString];
    
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    
    AFHTTPRequestOperation *operation = [[AFHTTPRequestOperation alloc] initWithRequest:request];
    operation.responseSerializer = [AFJSONResponseSerializer serializer];
    
    [operation setCompletionBlockWithSuccess:^(AFHTTPRequestOperation *operation, id responseObject) {
        NSLog(@"completion success");
        instagramJSONDic = (NSDictionary *)responseObject;
        [collectionView reloadData];
        
        //If this was a real app, I'd setup getting the the next URL for the photostream here.
        
    } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
        
        UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error receiving data from Instagram "
                                                            message:[error localizedDescription]
                                                           delegate:nil
                                                  cancelButtonTitle:@"Ok"
                                                  otherButtonTitles:nil];
        [alertView show];
    }];
    
    [operation start];
}




#pragma mark infastructure
-(IBAction)onTap:(id)sender{
    //Only called from the full screen imageView.
    [fullScreenImageView removeFromSuperview];
}



- (void)viewDidLoad {
    [super viewDidLoad];
    //CollectionView is setup in the Storyboard and displayed on the screen there.
    //The autolayout is also in the storyboard.
    
    instagramJSONDic = [NSDictionary new];
    fullScreenImageView = [[UIView alloc] init];
    self.view.backgroundColor = [UIColor blackColor];
    columnCount = 0;
    
    [collectionView registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:@"Cell"];
    
    [self startAFDownload]; //Start the HTTP requests calling it here, saves us a few miliseconds of display time.
    
    //In a real app, I'd put a fancy loading screen here.
}

-(void)viewDidAppear:(BOOL)animated {
    
}


- (void)didReceiveMemoryWarning
{
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

@end
