
-- Data Cleaning Project

SELECT * FROM NashvilleHousingData.NashvilleHousing;

-- Standardize Date Format

SELECT STR_TO_DATE(SaleDate, '%m/%d/%Y') as SaleDate
FROM NashvilleHousingData.NashvilleHousing;

UPDATE NashvilleHousing
SET SaleDate = STR_TO_DATE(SaleDate, '%m/%d/%Y');

Update NashvilleHousing
Set PropertyAddress = NULL 
Where PropertyAddress = '';
 --------------------------------------------------------------------------------------------------------------------------

-- Populate Property Address data

Select *
From NashvilleHousingData.NashvilleHousing
-- Where PropertyAddress is null
order by ParcelID;

SELECT *
FROM NashvilleHousingData.NashvilleHousing a
JOIN NashvilleHousingData.NashvilleHousing b
	ON a.ParcelID = b.ParcelID
	AND a.UniqueID  <> b.UniqueID
    WHERE a.PropertyAddress IS NULL;
    
Select NashvilleHousing AS a
JOIN NashvilleHousing AS b 
	ON a.ParcelID = b.ParcelID
    AND a.UniqueID <> b.UniqueID
SET a.PropertyAddress = IFNULL(a.PropertyAddress, b.PropertyAddress)
WHERE a.PropertyAddress IS NULL;

Update NashvilleHousing AS a
JOIN NashvilleHousing AS b 
	ON a.ParcelID = b.ParcelID
    AND a.UniqueID <> b.UniqueID
SET a.PropertyAddress = IFNULL(a.PropertyAddress, b.PropertyAddress)
WHERE a.PropertyAddress IS NULL;

-------------------------------------------------------------------------------------------------------------------------
SELECT ParcelID
FROM NashvilleHousing
WHERE ParcelID = ''
;

Update NashvilleHousing
SET OwnerAddress = NULL
WHERE OwnerAddress = ''
;


--------------------------------------------------------------------------------------------------------------------------

-- Breaking out Address into Individual Columns (Address, City, State)

Select PropertyAddress
From NashvilleHousing
-- Where PropertyAddress is null
-- order by ParcelID
;

Select *
From NashvilleHousing
-- Where PropertyAddress is null
-- order by ParcelID
;

ALTER TABLE NashvilleHousing
Add PropertySplitAddress varchar(255);

Select substring_index(OwnerAddress, ' ', 4) as OwnerStreetAddress
-- ,substring_Index(OwnerAddress, ' ',-2) as PropertyCity
-- ,substring_Index(OwnerAddress, ' ', -1) as OwnerState
From NashvilleHousing;

Select substring_index(OwnerCity, ' ', 1) as OwnerCity
From NashvilleHousing;


ALTER TABLE NashvilleHousing

ADD OwnerSplitCity varchar(255),
ADD OwnerSplitState varchar(255);

Update NashvilleHousing
SET OwnerSplitCity = substring_Index(OwnerSplitCity, ' ', 1)
;

ALTER TABLE NashvilleHousing
DROP OwnerSplitCity   ;

Update NashvilleHousing
SET OwnerSplitCity = substring_Index(OwnerAddress, ' ', -2);

SELECT substring_Index(OwnerSplitCity, ' ', 1)
FROM NashvilleHousing;

ALTER TABLE NashvilleHousing
DROP OwnerSplitState ;

Update NashvilleHousing
SET OwnerSplitState = substring_Index(OwnerAddress, ' ', -1);


Select *
From NashvilleHousing
-- Where PropertyAddress is null
-- order by ParcelID
;

Select OwnerSplitAddress
From NashvilleHousing
Where OwnerSplitAddress like '%%GOODLETTSVILLE';

Update NashvilleHousing
SET OwnerSplitAddress = trim(trailing 'HERMITAGE' from OwnerSplitAddress)
 ;
 
 
 --------------------------------------------------------------------------------------------------------------------------


-- Change Y and N to Yes and No in "Sold as Vacant" field


Select Distinct(SoldAsVacant), Count(SoldAsVacant)
From NashvilleHousing
Group by SoldAsVacant
order by 2;

Select SoldAsVacant
	,CASE When SoldAsVacant = 'Y' Then 'Yes'
    
		 When SoldAsVacant = 'N' Then 'No'
         ELSE SoldAsVacant
         End
From NashvilleHousing;


UPDATE NashvilleHousing 
SET 
    SoldAsVacant = CASE
        WHEN SoldAsVacant = 'Y' THEN 'Yes'
        WHEN SoldAsVacant = 'N' THEN 'No'
        ELSE SoldAsVacant
    END;



-- Remove Duplicates

WITH RowNumCTE AS(
Select *,
	ROW_NUMBER() OVER (
	PARTITION BY ParcelID,
				 PropertyAddress,
				 SalePrice,
				 SaleDate,
				 LegalReference
				 ORDER BY
					UniqueID
					) row_num

From NashvilleHousing)
-- order by ParcelID;

Select  *
FROM NashvilleHousing 
-- USING NashvilleHousing 
-- JOIN RowNumCTE ON NashvilleHousing.ParcelID = RowNumCTE.ParcelID
WHERE row_num > 1
-- Order by PropertyAddress
;


---------------------------------------------------------------------------------------------------------

-- Delete Unused Columns



Select *
From NashvilleHousing;


ALTER TABLE NashvilleHousing
DROP Column OwnerAddress;

ALTER TABLE NashvilleHousing
DROP Column TaxDistrict;

ALTER TABLE NashvilleHousing
DROP Column PropertyAddress;

ALTER TABLE NashvilleHousing
DROP Column SaleDate ;
